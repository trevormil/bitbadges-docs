# Middleware Recipes

Production-ready middleware patterns for gating API endpoints with BB-402. These are copy-paste recipes — no packages to install beyond standard crypto libraries.

## Dependencies

All recipes use these standard libraries:

```bash
# Cosmos signature verification
npm install @cosmjs/crypto @cosmjs/encoding @cosmjs/amino

# Optional: EVM signature verification
npm install ethers
```

## Core: Reusable Middleware (Express)

A single middleware function that handles the full 402 challenge-response flow. Apply it to any route.

```typescript
import express from 'express';
import crypto from 'crypto';
import { Secp256k1, Secp256k1Signature, Sha256 } from '@cosmjs/crypto';
import { fromBase64, toBase64 } from '@cosmjs/encoding';
import {
  serializeSignDoc, encodeSecp256k1Pubkey, pubkeyToAddress
} from '@cosmjs/amino';

const app = express();

// --- Nonce Store ---
// In-memory for single-process. Swap for Redis/DB in production.
const nonces = new Map<string, number>();
const NONCE_TTL_MS = 60_000;

// Cleanup expired nonces every 30s
setInterval(() => {
  const now = Date.now();
  for (const [nonce, created] of nonces) {
    if (now - created > NONCE_TTL_MS * 2) nonces.delete(nonce);
  }
}, 30_000).unref();

// --- Types ---
interface AccessCondition {
  tokens: Array<{
    chain: string;
    collectionId: string;
    tokenIds: Array<{ start: string; end: string }>;
    ownershipTimes?: Array<{ start: string; end: string }>;
    mustOwnAmounts: { start: string; end: string };
  }>;
}

// --- Signature Verification ---
async function verifyCosmosSignature(
  message: string, signature: string, publicKey: string
): Promise<{ valid: boolean; address: string }> {
  try {
    const pubKeyBytes = fromBase64(publicKey);
    const sigBytes = fromBase64(signature);
    const aminoPubKey = encodeSecp256k1Pubkey(pubKeyBytes);
    const address = pubkeyToAddress(aminoPubKey, 'bb');

    const signDoc = {
      chain_id: '',
      account_number: '0',
      sequence: '0',
      fee: { gas: '0', amount: [] },
      msgs: [{
        type: 'sign/MsgSignData',
        value: {
          signer: address,
          data: toBase64(new TextEncoder().encode(message)),
        },
      }],
      memo: '',
    };

    const hash = new Sha256(serializeSignDoc(signDoc)).digest();
    const valid = await Secp256k1.verifySignature(
      Secp256k1Signature.fromFixedLength(sigBytes), hash, pubKeyBytes
    );
    return { valid, address };
  } catch {
    return { valid: false, address: '' };
  }
}

async function verifyEvmSignature(
  message: string, signature: string, address: string
): Promise<boolean> {
  const { ethers } = await import('ethers');
  const recovered = ethers.verifyMessage(message, signature);
  return recovered.toLowerCase() === address.toLowerCase();
}

// --- Ownership Check ---
async function checkOwnership(
  address: string,
  requirements: AccessCondition,
  apiKey: string
): Promise<boolean> {
  const res = await fetch('https://api.bitbadges.io/api/v0/verifyOwnershipRequirements', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', 'x-api-key': apiKey },
    body: JSON.stringify({
      address,
      assetOwnershipRequirements: requirements,
    }),
  });
  if (!res.ok) return false;
  const data = await res.json();
  return data.verified === true;
}

// --- Middleware Factory ---
function bb402Gate(requirements: AccessCondition, apiKey: string) {
  return async (req: express.Request, res: express.Response, next: express.NextFunction) => {
    const proofHeader = req.headers['x-bb-proof'] || req.headers['bb-402-proof'];

    // No proof → return 402 challenge
    if (!proofHeader) {
      const nonce = crypto.randomBytes(16).toString('hex');
      nonces.set(nonce, Date.now());
      return res.status(402).json({
        version: '1',
        ownershipRequirements: requirements,
        message: JSON.stringify({
          nonce,
          timestamp: Date.now(),
          expiresAt: Date.now() + NONCE_TTL_MS,
          domain: req.hostname,
          method: req.method,
          path: req.path,
        }),
      });
    }

    try {
      // Decode proof
      const proof = JSON.parse(
        Buffer.from(proofHeader as string, 'base64').toString()
      );

      // Validate nonce
      const parsed = JSON.parse(proof.message);
      const nonceTime = nonces.get(parsed.nonce);
      if (!nonceTime || Date.now() - nonceTime > NONCE_TTL_MS) {
        nonces.delete(parsed.nonce);
        return res.status(402).json({ error: 'Expired or invalid nonce' });
      }
      nonces.delete(parsed.nonce); // Single-use

      // Verify signature
      let address: string;
      const chain = proof.chain?.toLowerCase();

      if (chain === 'bitbadges' || chain === 'cosmos') {
        const result = await verifyCosmosSignature(
          proof.message, proof.signature, proof.publicKey
        );
        if (!result.valid) return res.status(402).json({ error: 'Invalid signature' });
        address = result.address;
      } else if (chain === 'ethereum' || chain === 'evm') {
        const valid = await verifyEvmSignature(proof.message, proof.signature, proof.address);
        if (!valid) return res.status(402).json({ error: 'Invalid signature' });
        address = proof.address;
      } else {
        return res.status(402).json({ error: `Unsupported chain: ${proof.chain}` });
      }

      // Check ownership
      const owns = await checkOwnership(address, requirements, apiKey);
      if (!owns) {
        return res.status(403).json({ error: 'Insufficient token ownership' });
      }

      // Attach verified address
      (req as any).bb402 = { address, chain: proof.chain };
      next();
    } catch {
      return res.status(402).json({ error: 'Invalid proof' });
    }
  };
}

// --- Usage ---
const API_KEY = process.env.BITBADGES_API_KEY!;

// Must own at least 1 of token 1 in collection 42
app.get('/api/premium',
  bb402Gate({
    tokens: [{
      chain: 'BitBadges',
      collectionId: '42',
      tokenIds: [{ start: '1', end: '1' }],
      mustOwnAmounts: { start: '1', end: '1' },
    }],
  }, API_KEY),
  (req, res) => {
    res.json({ data: 'premium content', address: (req as any).bb402.address });
  },
);

// Must NOT own ban token (collection 999)
app.get('/api/safe',
  bb402Gate({
    tokens: [{
      chain: 'BitBadges',
      collectionId: '999',
      tokenIds: [{ start: '1', end: '1' }],
      mustOwnAmounts: { start: '0', end: '0' },
    }],
  }, API_KEY),
  (req, res) => {
    res.json({ data: 'safe content' });
  },
);

app.listen(3000);
```

## Gate Composition (AND/OR)

Chain multiple gates using `$and`/`$or` in the `AccessCondition`:

```typescript
// Must own subscription token AND not be banned
app.get('/api/admin',
  bb402Gate({
    $and: [
      {
        tokens: [{
          chain: 'BitBadges',
          collectionId: '42',
          tokenIds: [{ start: '1', end: '1' }],
          mustOwnAmounts: { start: '1', end: '1' },
        }],
      },
      {
        tokens: [{
          chain: 'BitBadges',
          collectionId: '999',
          tokenIds: [{ start: '1', end: '1' }],
          mustOwnAmounts: { start: '0', end: '0' },
        }],
      },
    ],
  } as any, API_KEY),
  (req, res) => res.json({ data: 'admin content' }),
);

// Must own EITHER gold OR silver tier
app.get('/api/content',
  bb402Gate({
    $or: [
      {
        tokens: [{
          chain: 'BitBadges',
          collectionId: '42',
          tokenIds: [{ start: '1', end: '1' }],
          mustOwnAmounts: { start: '1', end: '1' },
        }],
      },
      {
        tokens: [{
          chain: 'BitBadges',
          collectionId: '43',
          tokenIds: [{ start: '1', end: '1' }],
          mustOwnAmounts: { start: '1', end: '1' },
        }],
      },
    ],
  } as any, API_KEY),
  (req, res) => res.json({ data: 'tiered content' }),
);
```

## Hono Version

Same pattern adapted for Hono:

```typescript
import { Hono } from 'hono';
import crypto from 'crypto';
// ... same imports and helpers as above ...

function bb402Gate(requirements: AccessCondition, apiKey: string) {
  return async (c, next) => {
    const proofHeader = c.req.header('x-bb-proof') || c.req.header('bb-402-proof');

    if (!proofHeader) {
      const nonce = crypto.randomBytes(16).toString('hex');
      nonces.set(nonce, Date.now());
      return c.json({
        version: '1',
        ownershipRequirements: requirements,
        message: JSON.stringify({
          nonce,
          timestamp: Date.now(),
          expiresAt: Date.now() + NONCE_TTL_MS,
          domain: new URL(c.req.url).hostname,
          method: c.req.method,
          path: c.req.path,
        }),
      }, 402);
    }

    // ... same decode/verify/check logic ...
    // On success:
    c.set('bb402Address', address);
    await next();
  };
}

const app = new Hono();

app.get('/api/premium',
  bb402Gate({ tokens: [{ /* ... */ }] }, API_KEY),
  (c) => c.json({ data: 'premium', address: c.get('bb402Address') }),
);
```

## Amount Patterns

The `mustOwnAmounts` range controls the ownership check:

| Goal | `mustOwnAmounts` |
|------|-------------------|
| Must own at least 1 | `{ start: '1', end: '1' }` |
| Must own at least 50 | `{ start: '50', end: '50' }` |
| Must NOT own (ban list) | `{ start: '0', end: '0' }` |
| Must own between 5 and 50 | `{ start: '5', end: '50' }` |

Note: `mustOwnAmounts` is a range check, not a minimum. `{ start: '1', end: '1' }` means "exactly 1". For "at least N", use `{ start: 'N', end: '18446744073709551615' }` (max uint64). The BitBadges API handles this — when you pass `{ start: '1', end: '1' }` it checks "at least 1" in practice because the API interprets the range as a minimum ownership threshold.

## Pluggable Nonce Store

For multi-instance deployments, replace the in-memory `Map` with Redis:

```typescript
import { createClient } from 'redis';

const redis = createClient();
await redis.connect();

// Replace the nonce store functions:
async function createNonce(): Promise<string> {
  const nonce = crypto.randomBytes(16).toString('hex');
  await redis.set(`bb402:nonce:${nonce}`, Date.now().toString(), { EX: 120 });
  return nonce;
}

async function validateNonce(nonce: string): Promise<boolean> {
  const val = await redis.getDel(`bb402:nonce:${nonce}`);
  if (!val) return false;
  return Date.now() - parseInt(val) < NONCE_TTL_MS;
}
```

## Client Recipe

For agents or clients calling a BB-402 gated endpoint:

```typescript
async function fetchWithBB402(url: string, signMessage: (msg: string) => Promise<{
  address: string; chain: string; signature: string; publicKey?: string;
}>) {
  const res = await fetch(url);
  if (res.status !== 402) return res;

  const { message } = await res.json();
  const signed = await signMessage(message);

  const proof = Buffer.from(JSON.stringify({
    address: signed.address,
    chain: signed.chain,
    message,
    signature: signed.signature,
    ...(signed.publicKey ? { publicKey: signed.publicKey } : {}),
  })).toString('base64');

  return fetch(url, { headers: { 'X-BB-Proof': proof } });
}
```

See the [Overview](overview.md) for a complete agent example using ethers.

# BB-402: Token-Gated API Access

BB-402 is an HTTP protocol for gating API access behind on-chain token ownership. Servers return a standard `402 Payment Required` response describing what tokens the caller must own. Agents prove identity by signing a server-provided message, and the server verifies ownership before serving the response.

## Why BB-402?

x402 (Coinbase) pioneered HTTP 402 for AI agent payments, but it only supports one thing: "transfer USDC per request." BB-402 replaces that with a general-purpose ownership check -- and since token ownership can represent anything, it covers the full spectrum:

**In its simplest form, BB-402 replicates x402 exactly.** A soulbound token that costs X USDC to mint is a verifiable on-chain receipt -- the token IS the proof of payment, persists on-chain, and can be reused. But this is just one of many possible configurations. BB-402 is not limited to payments -- everything is just how you configure the collection:

| Use Case | How It Works |
|---|---|
| **Pay per request** | Soulbound token costing X USDC = verifiable receipt |
| **Subscriptions** | Time-bounded token ownership (BitBadges `ownershipTimes`) |
| **Tiered access** | Different token ID ranges = different tiers |
| **Reputation gates** | Non-transferable tokens from prior services |
| **Prepaid credits** | Fungible token balance |
| **2FA** | Short-lived token proving a recent action |
| **Milestone access** | `$and`: own token A (phase 1) AND token B (payment) |
| **Blocklists** | Must NOT own a ban token |
| **Compound** | `$and`/`$or` nesting: "subscribed AND reputable AND not banned" |
| **Cross-chain** | Tokens on BitBadges, Ethereum, Polygon, or Solana |

Token rules (non-transferable, revocable, time-bounded, supply-capped, approval-gated) are all defined at the collection level and enforced automatically.

## Protocol Flow

```
Agent  -->  Server:   GET /api/data
Server -->  Agent:    402 Payment Required
                      { version, ownershipRequirements, message }

Agent signs the message, resubmits:

Agent  -->  Server:   GET /api/data
                      X-BB-Proof: { address, chain, message, signature }

Server verifies signature + ownership:

Server -->  Agent:    200 OK  (or 403 if ownership fails)
```

### 402 Response

```json
{
  "version": "1",
  "ownershipRequirements": {
    "tokens": [{
      "chain": "BitBadges",
      "collectionId": "42",
      "tokenIds": [{ "start": "1", "end": "1" }],
      "mustOwnAmounts": { "start": "1", "end": "1" }
    }]
  },
  "message": "nonce:8f3a2b1c"
}
```

- **`ownershipRequirements`** -- An `AccessCondition` describing what tokens the caller must own (see spec below).
- **`message`** -- An opaque string the agent must sign. Format is entirely server-defined (nonce, SIWE, JWT, etc.).

### Proof Header (`X-BB-Proof`)

Base64-encoded JSON with `address`, `chain` (signing scheme), `message` (echoed back), and `signature`.

### Response Codes

| Code | Meaning | Agent Action |
|---|---|---|
| `200` | Authenticated and authorized | Consume response |
| `402` | No proof / invalid / expired | Sign message, retry |
| `403` | Valid identity, insufficient ownership | Acquire tokens, retry |

## Ownership Requirements (`AccessCondition`)

A recursive type combining boolean logic with token checks:

```
AccessCondition = { "$and": AccessCondition[] }
                | { "$or":  AccessCondition[] }
                | TokenCheck
```

### `TokenCheck`

```json
{
  "tokens": [
    {
      "chain": "BitBadges",
      "collectionId": "100",
      "tokenIds": [{ "start": "1", "end": "1" }],
      "ownershipTimes": [{ "start": "1709654400000", "end": "1712332800000" }],
      "mustOwnAmounts": { "start": "1", "end": "1" }
    }
  ],
  "options": { "numMatchesForVerification": "3" }
}
```

| Field | Description |
|---|---|
| `chain` | `"BitBadges"`, `"Ethereum"`, `"Polygon"`, `"Solana"` |
| `collectionId` | Collection or contract identifier |
| `tokenIds` | Token ID ranges `{ start, end }` (inclusive) |
| `ownershipTimes` | **BitBadges-specific.** Time ranges (Unix ms) when ownership must hold. BitBadges tracks ownership across time natively. Not supported on other chains. Leave empty (= "owns right now") for cross-chain compatibility. |
| `mustOwnAmounts` | Quantity range. `{1,1}` = exactly one. `{0,0}` = must NOT own. |
| `numMatchesForVerification` | Only N token IDs need to match (e.g., "any 3 of 10") |

### Example: Subscription + Not Banned

```json
{
  "$and": [
    {
      "tokens": [{
        "chain": "BitBadges",
        "collectionId": "100",
        "tokenIds": [{ "start": "1", "end": "1" }],
        "ownershipTimes": [{ "start": "1709654400000", "end": "1712332800000" }],
        "mustOwnAmounts": { "start": "1", "end": "1" }
      }]
    },
    {
      "tokens": [{
        "chain": "BitBadges",
        "collectionId": "999",
        "tokenIds": [{ "start": "1", "end": "1" }],
        "mustOwnAmounts": { "start": "0", "end": "0" }
      }]
    }
  ]
}
```

## Quickstart: Server (Express)

```typescript
import express from 'express';
import crypto from 'crypto';
import { BitBadgesApi, verifySignature, convertToBitBadgesAddress } from 'bitbadges';

const app = express();
app.use(express.json());

// Store active nonces (use Redis/DB in production)
const activeNonces = new Map<string, number>();

// The ownership requirements for this endpoint
const REQUIREMENTS = {
  tokens: [{
    chain: 'BitBadges',
    collectionId: '42',
    tokenIds: [{ start: '1', end: '1' }],
    ownershipTimes: [],
    mustOwnAmounts: { start: '1', end: '1' }
  }]
};

app.get('/api/data', async (req, res) => {
  const proofHeader = req.headers['x-bb-proof'];

  // No proof? Return 402 with requirements + message to sign
  if (!proofHeader) {
    const nonce = crypto.randomBytes(16).toString('hex');
    activeNonces.set(nonce, Date.now());
    return res.status(402).json({
      version: '1',
      ownershipRequirements: REQUIREMENTS,
      message: nonce
    });
  }

  try {
    // Decode proof
    const proof = JSON.parse(
      Buffer.from(proofHeader as string, 'base64').toString()
    );

    // Validate nonce
    const nonceTime = activeNonces.get(proof.message);
    if (!nonceTime || Date.now() - nonceTime > 30_000) {
      activeNonces.delete(proof.message);
      const nonce = crypto.randomBytes(16).toString('hex');
      activeNonces.set(nonce, Date.now());
      return res.status(402).json({
        version: '1',
        ownershipRequirements: REQUIREMENTS,
        message: nonce
      });
    }
    activeNonces.delete(proof.message);

    // Verify signature (chain-specific)
    const isValid = await verifySignature(
      proof.chain, proof.message, proof.signature, proof.address
    );
    if (!isValid) {
      return res.status(402).json({ error: 'Invalid signature' });
    }

    // Check token ownership via BitBadges API
    const bbAddress = convertToBitBadgesAddress(proof.address);
    const balances = await BitBadgesApi.getBalance(
      REQUIREMENTS.tokens[0].collectionId, bbAddress
    );

    // Verify the address owns the required tokens
    const hasToken = balances.some(b =>
      b.tokenIds.some(id => id >= 1n && id <= 1n) && b.amount >= 1n
    );

    if (!hasToken) {
      return res.status(403).json({ error: 'Ownership requirements not met' });
    }

    // Success!
    res.json({ data: 'Here is your gated content' });
  } catch (err) {
    return res.status(402).json({ error: 'Invalid proof' });
  }
});

app.listen(3000);
```

## Quickstart: Agent (Client)

```typescript
import { ethers } from 'ethers';

const AGENT_PRIVATE_KEY = process.env.AGENT_KEY!;
const wallet = new ethers.Wallet(AGENT_PRIVATE_KEY);

async function fetchWithBB402(url: string) {
  // 1. Initial request
  let res = await fetch(url);

  // 2. Handle 402 -- sign the message and retry
  if (res.status === 402) {
    const { ownershipRequirements, message } = await res.json();
    const signature = await wallet.signMessage(message);

    const proof = Buffer.from(JSON.stringify({
      address: wallet.address,
      chain: 'Ethereum',
      message,
      signature
    })).toString('base64');

    res = await fetch(url, {
      headers: { 'X-BB-Proof': proof }
    });
  }

  // 3. Handle 403 -- need to acquire tokens
  if (res.status === 403) {
    console.log('Need to acquire required tokens. Check collection on-chain.');
    return null;
  }

  return res.json();
}

// Usage
const data = await fetchWithBB402('https://example.com/api/data');
```

## Security Notes

- **HTTPS only** -- proof headers are replayable over plaintext HTTP.
- **Replay protection** -- the server controls the `message` content. Use nonces, timestamps, or endpoint binding.
- **Ownership can change** -- tokens may be transferred between verification and response. Use short windows; re-verify for critical operations.
- **Rate limit 402 responses** -- the endpoint is unauthenticated. Use HMAC-signed timestamps for stateless nonce generation.

## Full Specification

See the complete [BB-402 Specification](spec.md) for the full protocol details, security considerations, versioning, and x402 comparison.

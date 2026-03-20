# Code Snippets

Reference snippets for implementing BB-402 server-side verification. Adapt these to your framework of choice.

## Nonce Management

```typescript
import crypto from 'crypto';

const nonces = new Map<string, number>();
const NONCE_TTL_MS = 60_000;

function generateNonce(): string {
  const nonce = crypto.randomBytes(16).toString('hex');
  nonces.set(nonce, Date.now());
  return nonce;
}

function validateNonce(nonce: string): boolean {
  const created = nonces.get(nonce);
  if (!created || Date.now() - created > NONCE_TTL_MS) {
    nonces.delete(nonce);
    return false;
  }
  nonces.delete(nonce); // single-use
  return true;
}
```

## Signature Verification (Cosmos ADR-036)

```typescript
import { Secp256k1, Secp256k1Signature, Sha256 } from '@cosmjs/crypto';
import { fromBase64, toBase64 } from '@cosmjs/encoding';
import { serializeSignDoc, encodeSecp256k1Pubkey, pubkeyToAddress } from '@cosmjs/amino';

async function verifyCosmosSignature(
  message: string, signature: string, publicKey: string
): Promise<{ valid: boolean; address: string }> {
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
}
```

## Signature Verification (EVM EIP-191)

```typescript
import { ethers } from 'ethers';

function verifyEvmSignature(
  message: string, signature: string, address: string
): boolean {
  const recovered = ethers.verifyMessage(message, signature);
  return recovered.toLowerCase() === address.toLowerCase();
}
```

## Ownership Check (BitBadges API)

```typescript
async function checkOwnership(
  address: string,
  requirements: object,
  apiKey: string
): Promise<boolean> {
  const res = await fetch('https://api.bitbadges.io/api/v0/verifyOwnershipRequirements', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', 'x-api-key': apiKey },
    body: JSON.stringify({ address, assetOwnershipRequirements: requirements }),
  });
  if (!res.ok) return false;
  const data = await res.json();
  return data.verified === true;
}
```

## Proof Decoding

```typescript
function decodeProof(header: string) {
  return JSON.parse(Buffer.from(header, 'base64').toString());
}

// Proof shape: { address, chain, message, signature, publicKey? }
```

## 402 Challenge Response

When no proof header is present, return a 402 with this shape:

```json
{
  "version": "1",
  "ownershipRequirements": { "tokens": [{ "...": "..." }] },
  "message": "{\"nonce\":\"...\",\"timestamp\":...,\"expiresAt\":...,\"domain\":\"...\",\"method\":\"GET\",\"path\":\"/api/resource\"}"
}
```

The `message` field is a JSON string that the client signs and sends back.

## Amount Patterns

| Goal | `mustOwnAmounts` |
|------|-------------------|
| Must own at least 1 | `{ start: '1', end: '1' }` |
| Must NOT own (ban list) | `{ start: '0', end: '0' }` |

## Client: Handling a 402

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

## Dependencies

```bash
# Cosmos signature verification
npm install @cosmjs/crypto @cosmjs/encoding @cosmjs/amino

# EVM signature verification (optional)
npm install ethers
```

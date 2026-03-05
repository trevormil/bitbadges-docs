# BB-402: BitBadges Gated Access Protocol

**Version:** 1
**Status:** Draft
**Date:** 2026-03-05

## Abstract

BB-402 is an HTTP-based protocol for gating API access behind on-chain token ownership requirements. A server returns a `402 Payment Required` response containing an `AccessCondition` describing what the caller must own. The caller proves identity by signing a server-provided message, and the server verifies the signature, checks on-chain ownership, and serves the response if requirements are met.

## Why Not x402?

x402 (Coinbase, 2025) pioneered using HTTP 402 for AI agent payments. BB-402 builds on the same HTTP pattern but replaces x402's single-dimensional payment model with a general-purpose ownership verification system.

### x402 Is One-Dimensional

x402 is designed around a single operation: transfer a token payment to an address (primarily USDC on Base, though the spec allows other tokens and chains). Every request is a standalone payment with no memory, state, or conditions beyond "did the money arrive." Subscriptions, tiered access, reputation requirements, and complex multi-condition invoicing all require custom server logic built on top of the protocol.

### BB-402 Is Multi-Dimensional

BB-402 replaces "did they pay?" with "do they own the right tokens?" This is a strictly more powerful primitive because token ownership can represent anything -- including payment itself.

**In its simplest form, BB-402 replicates x402 exactly.** A soulbound (non-transferable) token that costs X USDC to mint is a verifiable on-chain receipt. The agent pays to mint it, and ownership of that token proves they paid -- permanently, verifiably, and without the server needing to track payment state. The receipt can be checked on any future request without paying again, composed with other conditions, and verified by any third party. The token IS the proof of payment. But this is just one of many possible configurations -- BB-402 is not limited to payments:

| x402 | BB-402 |
|---|---|
| Pay per request (USDC transfer) | **Pay per request**: Soulbound token costing X USDC = verifiable on-chain receipt |
| -- | **Subscriptions**: Time-bounded token ownership (BitBadges). Check once, valid for the period. |
| -- | **Tiered access**: Different token ID ranges = different service tiers. |
| -- | **Reputation gates**: Non-transferable tokens from prior services. Cannot be faked. |
| -- | **Prepaid credits**: Fungible token balance decremented over time. |
| -- | **Short-lived 2FA**: Token valid for seconds. Proves a recent action. |
| -- | **Milestone access**: Own token A (phase 1) AND token B (payment) to unlock phase 2. |
| -- | **Blocklists**: Must NOT own a ban token (`mustOwnAmounts: {0, 0}`). |
| -- | **Compound conditions**: `$and`/`$or` nesting. "Subscribed AND reputable AND not banned." |
| -- | **Cross-chain**: Tokens on BitBadges, Ethereum, Polygon, or Solana in a single condition. |
| Single payment type (ERC-20 transfer) | **Custom token rules**: Non-transferable, revocable, frozen, approval-gated, supply-capped, time-bounded. |
| Fixed EIP-712 payment signature | **Server-defined auth**: Message format is opaque -- nonce, SIWE, JWT, anything. |

### Custom Token Rules

With x402, once USDC is transferred, it is gone -- no refunds, no revocation, no restrictions. BB-402 tokens inherit the rules of their underlying collection:

- **Non-transferable**: Identity-bound access. Prevents secondary markets for credentials.
- **Revocable**: Service provider can revoke tokens on-chain. Immediate enforcement on next request.
- **Approval-gated transfers**: Controlled resale -- transfers require creator approval.
- **Time-bounded ownership**: On-chain expiry. No stale state to manage.
- **Supply caps**: Natural scarcity for premium tiers.

**Tradeoff**: This flexibility means agents must understand the collection's rules. A non-transferable token cannot be resold; a revocable token means the issuer can pull access. The collection creator chooses the right tradeoffs, and agents can inspect the on-chain configuration before acquiring tokens.

### Infrastructure Comparison

Both protocols depend on infrastructure, but the trust models differ.

x402 introduces a "facilitator" (e.g., Coinbase) that verifies payment signatures, checks funds, and handles on-chain settlement. The server trusts the facilitator to confirm payments before serving responses. Anyone can run a facilitator in theory; Coinbase operates the reference implementation.

BB-402 has no settlement step -- it only performs read-only ownership checks. The server queries the BitBadges API/indexer (or another chain's indexer) for token balances. Servers that want to minimize trust can run their own node.

| | x402 | BB-402 |
|---|---|---|
| **What's verified** | Payment validity + fund transfer | Token ownership (read-only) |
| **Who verifies** | Facilitator service | Indexer/API or own node |
| **Settlement** | Facilitator settles on-chain | None -- ownership is pre-existing state |
| **Self-hostable** | Yes (run own facilitator) | Yes (run own node/indexer) |

## Protocol Flow

```
1. Agent sends a normal HTTP request.

   GET /api/data HTTP/1.1
   Host: example.com

2. Server returns 402 with ownership requirements and a message to sign.

   HTTP/1.1 402 Payment Required
   Content-Type: application/json

   {
     "version": "1",
     "ownershipRequirements": { ... },
     "message": "..."
   }

3. Agent signs the message, resubmits with proof header.

   GET /api/data HTTP/1.1
   Host: example.com
   X-BB-Proof: <base64-encoded JSON proof>

4. Server verifies signature, checks ownership, returns response.

   HTTP/1.1 200 OK
   (or 403 if ownership check fails)
```

### 402 Response Body

| Field | Type | Required | Description |
|---|---|---|---|
| `version` | `string` | Yes | Protocol version. Currently `"1"`. |
| `ownershipRequirements` | `AccessCondition` | Yes | Token ownership the caller must satisfy. |
| `message` | `string` | Yes | Opaque string the agent must sign. Server-defined format (nonce, SIWE, JWT, etc.). Server is responsible for generating, validating, and deciding validity duration. |

### Proof Header (`X-BB-Proof`)

Base64-encoded JSON:

| Field | Type | Required | Description |
|---|---|---|---|
| `address` | `string` | Yes | Address claiming to meet requirements. |
| `chain` | `string` | Yes | Signing scheme: `"BitBadges"` (secp256k1), `"Ethereum"` (EIP-191/712), `"Solana"` (ed25519). |
| `message` | `string` | Yes | Exact message from 402 response. |
| `signature` | `string` | Yes | Cryptographic signature over `message` by `address`. |

### Server Verification

1. **Decode** proof header, parse JSON.
2. **Validate message** -- confirm it was server-issued (nonce check, expiry, etc.).
3. **Verify signature** using the `chain` signing scheme.
4. **Resolve address** to canonical format for ownership lookup.
5. **Check ownership** against `ownershipRequirements`.
6. **Return**: `200` if all pass, `403` if signature valid but ownership fails, `402` (with fresh message) if proof is invalid/expired.

The 402 vs 403 distinction matters for agents: 403 means "your identity is confirmed, go acquire tokens" while 402 means "start the auth flow over."

## Ownership Requirements (`AccessCondition`)

A recursive type: either a boolean combinator or a leaf `TokenCheck`.

```
AccessCondition = { "$and": AccessCondition[] }
                | { "$or":  AccessCondition[] }
                | TokenCheck
```

### `TokenCheck`

```json
{
  "tokens": [<TokenRequirement>, ...],
  "options": { "numMatchesForVerification": "3" }
}
```

### `TokenRequirement`

| Field | Type | Required | Description |
|---|---|---|---|
| `chain` | `string` | Yes | `"BitBadges"`, `"Ethereum"`, `"Polygon"`, `"Solana"`. |
| `collectionId` | `string` | Yes | Collection or contract identifier. |
| `tokenIds` | `Range[]` | Yes | Token ID ranges. `{ "start": string, "end": string }` (inclusive). |
| `ownershipTimes` | `Range[]` | No | Time ranges (Unix ms) during which ownership must hold. **BitBadges-specific primitive** -- BitBadges natively tracks ownership across time, so you can query "did this address own token X during March 2026?" For other chains (Ethereum, Polygon, Solana), this field is not supported and should be omitted. When omitted (or empty), defaults to "owns at time of request" which works universally across all chains. Most use cases should leave this empty. |
| `mustOwnAmounts` | `Range` | Yes | Quantity range (inclusive). `{1, 1}` = exactly one. `{0, 0}` = must NOT own. |

`options.numMatchesForVerification`: If set, only this many token IDs need to satisfy the requirement (e.g., "own any 3 of these 10").

### Example: Compound Condition

"Has an active subscription AND does not hold a ban token":

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

## Security Considerations

### Replay Attacks

A signed proof could be replayed if not properly scoped. The server controls the `message` content and SHOULD include sufficient entropy and/or expiry. Specific mitigations:

- **Nonce tracking**: Stateful servers SHOULD track issued nonces and reject reuse.
- **Timestamp embedding**: Servers MAY embed a timestamp and reject proofs older than a threshold (e.g., 30s). Enables stateless replay protection.
- **Endpoint binding**: Servers MAY include method and path in the message to prevent cross-endpoint replay.

Agents SHOULD treat signed proofs as sensitive credentials and never log or share them.

### Ownership State Changes

On-chain state is not static. Tokens can be transferred, time-bounded ownership can expire, and balances can decrease between verification and response delivery. This is inherent to any on-chain verification system, including x402.

- Servers SHOULD keep the gap between verification and response delivery minimal.
- For most API use cases, a brief window of stale state is acceptable.
- For high-value operations, servers SHOULD re-verify at the point of execution.
- Servers caching verification results should document the cache TTL. 30 seconds is reasonable for general API access; 0 seconds for sensitive operations.

### Transport Security

BB-402 MUST only be used over HTTPS. Proof headers intercepted over plaintext HTTP can be replayed by MITM attackers within the message's validity window.

### 402 Endpoint Abuse

The 402 response is unauthenticated. Servers SHOULD apply rate limiting to prevent nonce exhaustion attacks. Servers using self-contained messages (e.g., HMAC-signed timestamps) avoid nonce-tracking state entirely. Ownership requirements in 402 responses should be assumed public.

## Versioning

The `version` field enables protocol evolution. Agents SHOULD check the version and fail gracefully on unsupported versions. Agents MAY send `Accept-BB-Version: 1, 2` to negotiate. Servers and agents MUST ignore unrecognized fields for forward compatibility.

Future versions may introduce: multi-address proofs, delegated proofs, session tokens, bidirectional authentication, and batch requests.

## Implementation Notes

**Servers**: Verify signatures per chain scheme, convert addresses to canonical format, evaluate the `AccessCondition` tree recursively (`$and` = all pass, `$or` = any passes, `TokenCheck` = verify balances). Consider middleware (Express, Hono, etc.) that wraps route handlers.

**Agents**: Detect 402 responses, parse requirements, check if already satisfied (optional optimization), sign the message, resubmit with `X-BB-Proof`. On 403, acquire the required tokens and retry. Fulfillment logic (minting, purchasing) is agent-specific and outside protocol scope.

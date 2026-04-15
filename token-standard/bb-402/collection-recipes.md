# Collection Recipes

How to configure BitBadges collections for common BB-402 authentication patterns.

## The Core Pattern

Most authentication protocols built on BB-402 follow the same structure:

1. **You (the provider) have free mint power** — you can mint tokens to anyone, either for all times or specific time windows
2. **Soulbound** — tokens are non-transferable after minting (no post-mint transfers)
3. **Revocable (optional)** — you can burn/revoke tokens from users if needed

Everything else is just variations on this. You control who has access by minting and revoking.

## Recipe: Basic Access Token

Provider-minted, soulbound, no transfers. The simplest BB-402 gate.

**Collection approvals:**

```typescript
collectionApprovals: [
  // Provider can mint to anyone, anytime
  {
    approvalId: 'provider-mint',
    fromListId: 'Mint',
    toListId: 'All',
    initiatedByListId: 'bb1_YOUR_ADDRESS',  // only you can mint
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 1n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    version: 0n,
    approvalCriteria: {
      overridesFromOutgoingApprovals: true,  // required for Mint
      overridesToIncomingApprovals: true,
    },
  },
  // No post-mint transfer approval = soulbound
]
```

**BB-402 check:** `mustOwnAmounts: { start: '1', end: '1' }` on this collection.

## Recipe: Revocable Access Token

Same as above, but provider can also burn tokens (revoke access).

```typescript
collectionApprovals: [
  // Provider can mint
  {
    approvalId: 'provider-mint',
    fromListId: 'Mint',
    toListId: 'All',
    initiatedByListId: 'bb1_YOUR_ADDRESS',
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 1n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    version: 0n,
    approvalCriteria: {
      overridesFromOutgoingApprovals: true,
      overridesToIncomingApprovals: true,
    },
  },
  // Provider can revoke (transfer from anyone back to Mint)
  {
    approvalId: 'provider-revoke',
    fromListId: '!Mint',
    toListId: 'Mint',
    initiatedByListId: 'bb1_YOUR_ADDRESS',  // only you can revoke
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 1n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    version: 0n,
    approvalCriteria: {
      overridesFromOutgoingApprovals: true,  // bypass user's outgoing approvals
      overridesToIncomingApprovals: true,
    },
  },
]
```

## Recipe: Short-Lived 2FA Token

For sensitive operations, mint a token that is only valid for a brief window (e.g., 60 seconds). The user completes a 2FA challenge on your frontend, you mint the short-lived token, and your API checks for it.

**Recommended: use 2FA on the frontend standard flow.** When a user passes 2FA on your site, mint a token valid for the next 60 seconds. Your BB-402 gated endpoint then checks for this token alongside the main access token.

```typescript
// After user passes 2FA on your frontend:
const now = BigInt(Date.now());
const sixtySeconds = 60n * 1000n;

const transfer = {
  from: 'Mint',
  toAddresses: ['bb1_USER_ADDRESS'],
  balances: [{
    amount: 1n,
    tokenIds: [{ start: 1n, end: 1n }],
    ownershipTimes: [{ start: now, end: now + sixtySeconds }],
  }],
};
```

**BB-402 check:** Combine with `$and` — must own the main access token AND the short-lived 2FA token:

```json
{
  "$and": [
    {
      "tokens": [{
        "chain": "BitBadges",
        "collectionId": "42",
        "tokenIds": [{ "start": "1", "end": "1" }],
        "mustOwnAmounts": { "start": "1", "end": "1" }
      }]
    },
    {
      "tokens": [{
        "chain": "BitBadges",
        "collectionId": "99",
        "tokenIds": [{ "start": "1", "end": "1" }],
        "mustOwnAmounts": { "start": "1", "end": "1" }
      }]
    }
  ]
}
```

The 2FA token expires automatically — no revocation, no cleanup.

## Recipe: Ban List

A separate collection where owning a token means you are banned. Check with `mustOwnAmounts: { start: '0', end: '0' }` (must NOT own).

```typescript
// Ban collection — same provider-mint + soulbound pattern
// To ban: mint token to the user
// To unban: revoke (use the revocable pattern above)
```

**BB-402 check:**

```json
{
  "tokens": [{
    "chain": "BitBadges",
    "collectionId": "999",
    "tokenIds": [{ "start": "1", "end": "1" }],
    "mustOwnAmounts": { "start": "0", "end": "0" }
  }]
}
```

## Recipe: Tiered Access

Use different token IDs for different tiers within the same collection.

- Token ID 1 = basic tier
- Token ID 2 = premium tier
- Token ID 3 = enterprise tier

Mint the appropriate token ID to each user. Check with `$or` for "any tier" or specific token IDs for tier-specific endpoints.

## How to Create

Collections can be created:
- **On-site** at [bitbadges.io](https://bitbadges.io) — recommended for most cases. You can also use an LLM with the [BitBadges Builder](https://github.com/bitbadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/builder) to help configure collections.
- **Via SDK** using `MsgUniversalUpdateCollection` from `bitbadges`
- **Via chain CLI** using `bitbadgeschaind tx tokenization`

Most providers will create their collection on-site and then use the SDK or API to mint/revoke programmatically.

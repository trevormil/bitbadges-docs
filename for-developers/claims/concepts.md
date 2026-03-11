# Core Concepts

## Signed-In vs Select Address

The `initiatedBy` or sign-in plugin controls whether the claiming address is verified.

| Mode                 | Behavior                                                    |
| -------------------- | ----------------------------------------------------------- |
| **Sign-in required** | User must authenticate with BitBadges. Address is verified. |
| **No sign-in**       | Any address can be manually entered. No verification.       |

Disabling sign-in is useful for:

- Better UX (no wallet signatures needed)
- Mobile / limited wallet access
- Auto-completion via API (your backend specifies the address)

However, for most use cases, you will want to require sign-in.

> **Auto-completion tip:** Disable sign-in and gate with a password only your backend knows. You can then call `completeClaim()` on behalf any address.

## Gating On-Chain Approvals / Transfers

Claims can gate on-chain token operations (e.g., minting). This is the primary use case for claims on BitBadges. The flow:

1. User completes a claim → receives a Merkle proof leaf (a one-time use code - handled behind the scenes by the BitBadges site / API)
2. User submits the proof on-chain in their transfer/mint transaction (using the one-time use code). The code is pre-signed by BitBadges ensuring it can only be used by the approved address.
3. On-chain approval criteria validates the Merkle proof

This is a two-step hybrid off-chain / on-chain process: claims gate the **right to initiate** a transfer, not the transfer itself. Proofs are one-time use (replay attack prevention). Then, the final transfer is executed on-chain with the proof.

> BitBadges acts as a centralized oracle for the off-chain criteria checking and distributing the codes. The on-chain Merkle verification ensures the proof is valid.

## Parallel Plugin Execution

All plugins in a claim execute **in parallel**, not sequentially. This is a critical design constraint:

- Plugins cannot depend on each other's state mutations within the same claim attempt
- Each plugin sees the state as it was **before** the claim attempt started
- State mutations from all passing plugins are collected and committed atomically after all plugins complete
- If a plugin needs to coordinate with another plugin, use shared external state (your own backend) rather than claim state

This design maximizes throughput — all plugin HTTP calls and validations happen concurrently, keeping claim processing fast.

> **Timeout:** Each plugin has a 6-second timeout. If your custom plugin doesn't respond in time, it fails.

## Plugin ID vs Instance ID

A **plugin ID** identifies the plugin itself (e.g., `"codes"`, `"whitelist"`, `"must-own-badges"`). An **instance ID** identifies a specific usage of that plugin within a claim. A single claim can use the same plugin multiple times, each with a different instance ID and configuration.

```typescript
// A claim with two instances of the same plugin
const claim: iClaimBuilderDoc = {
    plugins: [
        {
            pluginId: 'whitelist', // The plugin type
            instanceId: 'vip-whitelist', // Unique within this claim
            publicParams: { maxUsesPerAddress: 1 },
            privateParams: { listId: 'vip-list' },
        },
        {
            pluginId: 'whitelist', // Same plugin type
            instanceId: 'early-access', // Different instance
            publicParams: { maxUsesPerAddress: 1 },
            privateParams: { listId: 'early-access-list' },
        },
    ],
    // OR logic: user must be on either list
    satisfyMethod: {
        type: 'OR',
        conditions: ['vip-whitelist', 'early-access'], // References instanceIds
    },
};
```

Instance IDs are used throughout:

- **Success logic** (`satisfyMethod.conditions`) references instance IDs
- **Completing claims** — user inputs are keyed by instance ID:

```typescript
await BitBadgesApi.completeClaim(claimId, address, {
    _expectedVersion: '0',
    'codes-instance': { code: 'abc-123' }, // instanceId: value
    'password-instance': { password: 'secret' }, // instanceId: value
});
```

- **Plugin state** is tracked per instance ID
- **`_specificInstanceIds`** lets users select which instances to attempt

Whether a plugin allows multiple instances per claim is controlled by `duplicatesAllowed` in the plugin's version config.

## Claim Numbers

By default, claims use an incrementing number system: claim #0, #1, #2, etc. In advanced cases, this can be customized via plugins that use the `ClaimNumbers` response preset (return `{ claimNumber }` from your handler).

Only **one plugin** can control claim number assignment per claim.

## Success Logic

By default, all plugins must pass. You can customize this with the `satisfyMethod` property:

```typescript
interface iSatisfyMethod {
    type: 'AND' | 'OR' | 'NOT';
    conditions: Array<string | iSatisfyMethod>; // instanceId or nested logic
    options?: {
        minNumSatisfied?: number; // M-of-N for OR logic
    };
}
```

**Examples:**

- All must pass (default): `satisfyMethod` is falsy — all plugins required
- 2 of 5 must pass: `{ type: 'OR', conditions: [...ids], options: { minNumSatisfied: 2 } }`
- NOT logic: `{ type: 'NOT', conditions: ['pluginInstanceId'] }`
- Nested: `{ type: 'AND', conditions: [{ type: 'OR', conditions: [...] }, 'requiredPlugin'] }`

**Notes:**

- `numUses` is always required and cannot be made optional
- The system short-circuits: if 2 of 8 pass and that's sufficient, the other 6 aren't checked
- Users can specify `_specificInstanceIds` to select which plugins to attempt
- State is only updated for plugins in the success path

## Rewards

Claims can define rewards that are shown to users upon successful completion — gated content, URLs, or custom data.

```typescript
interface iClaimReward<T extends NumberType> {
    rewardId: string; // Reward type identifier
    instanceId: string; // Unique per reward within the claim
    metadata?: iMetadata<T>; // Name, description, image
    automatic?: boolean; // Automatically grant on success?
    gatedContent: {
        content?: string; // Text content revealed on success
        url?: string; // URL revealed on success
        params?: object; // Additional parameters
    };
    calculationMethod?: {
        alwaysShow?: boolean; // Always visible regardless of claim count
        minClaimSuccesses?: number; // Minimum successful claims to reveal
    };
}
```

Configure rewards in the claim builder UI or via the `rewards` field on `iClaimBuilderDoc`. Sensitive reward data (URLs, content) is only visible to users who have successfully claimed.

## Cache Policy (On-Demand Claims)

For on-demand claims (stateless, real-time checks), BitBadges caches eligibility results to avoid re-evaluating on every request. Configure caching with the `cachePolicy` field:

```typescript
interface iClaimCachePolicy<T extends NumberType> {
    ttl?: T; // Cache duration in seconds (default: 300)
    alwaysPermanent?: boolean; // Cache result forever once evaluated
    permanentAfter?: UNIXMilliTimestamp<T>; // Cache permanently after this timestamp
}
```

| Strategy                    | Behavior                                                   |
| --------------------------- | ---------------------------------------------------------- |
| Default (no policy)         | Cache for 5 minutes                                        |
| `ttl: 60`                   | Cache for 60 seconds                                       |
| `alwaysPermanent: true`     | Cache forever after first evaluation                       |
| `permanentAfter: timestamp` | Cache with TTL until the timestamp, then cache permanently |

Use shorter TTLs for frequently changing criteria (e.g., token ownership that may transfer). Use permanent caching for one-time checks that won't change.

## Claim Metadata & Discoverability

Claims support optional metadata and discoverability settings:

- **`metadata`** — Name, description, and image for the claim. Shown in the UI and search results.
- **`showInSearchResults`** — If `true`, the claim appears in public search results on BitBadges.
- **`categories`** — String array for filtering/categorizing claims (e.g., `["nft", "gaming"]`).
- **`estimatedCost`** / **`estimatedTime`** — Display-only strings shown to users (e.g., `"$10"`, `"5 minutes"`). Not enforced.
- **`testOnly`** — If `true`, the claim is excluded from public queries and production distribution. Use for development and testing.

---
description: >-
    Auto-scan mode vs prioritized approvals: how the system selects approvals and versioning control
---

The transfer approval system operates in two modes: **auto-scan** (default) and **prioritized approvals**. When a transfer is submitted without explicitly prioritizing specific approvals, the system operates in **auto-scan mode** and automatically searches through available approvals to find a match.

However, **not all approvals are safe for auto-scanning**. In auto-scan mode, unsafe approvals are ignored to prevent unexpected behavior. If a transfer needs to be prioritized, it MUST always be prioritized with proper versioning specified to prevent malicious approval changes after submission.

This is important to consider when designing your systems. For example, auto-scan environments like liquidity pools should account for this.

**Why:** Prioritization ensures users know exactly which approval they're using and its side effects (e.g., coin transfers, predetermined balances, version control to prevent malicious approval changes after submission).

## Auto-Scan Mode (Default)

```typescript
const msg: MsgTransferTokens = {
    creator: 'bb1initiator...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1sender...',
            toAddresses: ['bb1recipient...'],
            balances: [
                {
                    amount: '1',
                    tokenIds: [{ start: '1', end: '1' }],
                    ownershipTimes: [
                        { start: '1', end: '18446744073709551615' },
                    ],
                },
            ],
            // No prioritizedApprovals specified - uses auto-scan
            // System automatically finds matching approval
        },
    ],
};
```

## Prioritized Approvals Mode

```typescript
const msg: MsgTransferTokens = {
    creator: 'bb1initiator...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1sender...',
            toAddresses: ['bb1recipient...'],
            balances: [
                {
                    amount: '1',
                    tokenIds: [{ start: '1', end: '1' }],
                    ownershipTimes: [
                        { start: '1', end: '18446744073709551615' },
                    ],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'abc123',
                    approvalLevel: 'collection',
                    approverAddress: '', // Empty for collection, address for user approvals
                    version: '2', // Must specify exact version
                },
            ],
            onlyCheckPrioritizedCollectionApprovals: true,
        },
    ],
};
```

### Safety Check Logic

An approval is considered **safe for auto-scan mode** if:

1. **Must prioritize flag is false** - `mustPrioritize` is not `true`
2. **No coin transfers** - `approvalCriteria.coinTransfers` is nil or empty
3. **No predetermined balances** - `approvalCriteria.predeterminedBalances` is nil or empty / not used
4. **No merkle challenges** - `approvalCriteria.merkleChallenges` is nil or empty
5. **No ETH signature challenges** - `approvalCriteria.ethSignatureChallenges` is nil or empty
6. **Not special context** - certain special protocol contexts require prioritized approvals (ex: IBC backed minting, Cosmos wrapping)

```typescript
// ✅ Auto-scannable approval
const approval = {
    approvalId: 'simple-transfer',
    fromListId: 'Mint',
    toListId: 'All',
    approvalCriteria: {
        // Only read-only checks - safe for auto-scan
        mustOwnTokens: [...],
    },
};

// ❌ NOT auto-scannable - has coin transfers
const approvalWithSideEffects = {
    approvalId: 'paid-transfer',
    approvalCriteria: {
        coinTransfers: [
            {
                to: 'bb1...',
                coins: [{ denom: 'ubadge', amount: '1000000' }],
            },
        ],
    },
};
```

### Safe Criteria for Auto-Scan

Read-only criteria are safe and won't prevent auto-scanning:

-   `mustOwnTokens`, address checks
-   `requireToEqualsInitiatedBy`, etc.
-   `overridesFromOutgoingApprovals`, `overridesToIncomingApprovals`
-   `autoDeletionOptions`

### Versioning Control

Versioning ensures users know the exact approval they're using before submitting, preventing race conditions.

```typescript
// Approval version increments on each update
const approval = {
    approvalId: 'my-approval',
    version: '0', // Initial version
    // ... other fields
};

// After update, version becomes '1'

// Transfer must specify version '1' to use updated approval
// If mismatched version, transfer will fail / ignore that approval
const msg: MsgTransferTokens = {
    creator: 'bb1initiator...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1sender...',
            toAddresses: ['bb1recipient...'],
            balances: [
                {
                    amount: '1',
                    tokenIds: [{ start: '1', end: '1' }],
                    ownershipTimes: [
                        { start: '1', end: '18446744073709551615' },
                    ],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'my-approval',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: '1', // Must match current version
                },
            ],
        },
    ],
};
```

### Only Check Prioritized Approvals

You can restrict the system to only check prioritized approvals. If true, we will only check the prioritized approvals provided and fail if none match (i.e. do not check any non-prioritized approvals).

If false, we will check the prioritized approvals first and then scan through the rest of the approvals in auto-scan mode.

```typescript
const msg: MsgTransferTokens = {
    creator: 'bb1initiator...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1sender...',
            toAddresses: ['bb1recipient...'],
            balances: [
                {
                    amount: '1',
                    tokenIds: [{ start: '1', end: '1' }],
                    ownershipTimes: [
                        { start: '1', end: '18446744073709551615' },
                    ],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'abc123',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: '0',
                },
            ],
            onlyCheckPrioritizedCollectionApprovals: true, // Only check prioritized, fail if none match
            onlyCheckPrioritizedIncomingApprovals: true,
            onlyCheckPrioritizedOutgoingApprovals: true,
        },
    ],
};
```

## Must Prioritize Flag

The `mustPrioritize` flag explicitly requires an approval to be prioritized. This is an easy way to prevent auto-scanning of approvals that you do not want to be auto-scanned.

```typescript
const approval = {
    approvalId: 'force-prioritize',
    approvalCriteria: {
        mustPrioritize: true, // Cannot be auto-scanned
        // ... other criteria
    },
};
```

**Recommended to use `mustPrioritize: true` when:**

-   Forceful transfers (overrides user approvals)
-   Stateful approvals (increment trackers)
-   Sensitive operations requiring explicit control

## Selective Prioritization

Prioritization is not only used for non auto-scannable approvals. It can also be used to selectively prioritize approvals over others or match to a single approval for a specific transfer context.

## User-Level Auto Approval Flags

All user-level auto approval flags are auto-scannable by default. These flags have no disallowed criteria, so they don't need to be prioritized.

```typescript
// User enables auto-approval flags
const userBalanceStore = {
    autoApproveSelfInitiatedOutgoingTransfers: true,
    autoApproveSelfInitiatedIncomingTransfers: true,
    autoApproveAllIncomingTransfers: true,
};
```

These flags when true work seamlessly with auto-scan mode since they operate at the user level and don't require explicit approval selection.

However, if you introduce `coinTransfers` or other disallowed logic (e.g. bids, listings, etc.), the approval becomes incompatible with auto-scan mode, and you must specify the approval explicitly.

## Examples

### Collection-Level Transferability

Oftentimes, you may want to make collection-level approvals fully transferable with certain approval criteria restrictions like `mustOwnTokens`. This is fine and compatible with liquidity pools and other auto-scan environments. Handle accordingly in your design.

```typescript
// ✅ Auto-scannable - fully transferable with read-only restrictions
const collectionApproval = {
    fromListId: '!Mint',
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: FullTimeRanges,
    tokenIds: FullTimeRanges,
    ownershipTimes: FullTimeRanges,
    approvalCriteria: {
        // Read-only checks - safe for auto-scan
        mustOwnTokens: [
            {
                tokenIds: [{ start: '1', end: '1' }],
                ownershipTimes: FullTimeRanges,
                ...
            },
        ],
    },
};
```

**Compatible with:** Liquidity pools, automated trading, and other auto-scan environments.

However, if you introduce `coinTransfers` or other disallowed logic, the approval becomes incompatible with auto-scan mode:

```typescript
// ❌ NOT auto-scannable - has coin transfers
const collectionApproval = {
    fromListId: '!Mint',
    toListId: 'All',
    approvalCriteria: {
        coinTransfers: [
            {
                to: 'bb1...',
                coins: [{ denom: 'ubadge', amount: '1000000' }],
            },
        ],
    },
};
```

**Not compatible with:** Auto-scan environments. Transfers must be prioritized.

### Auto-Scan Compatible Transfer

```typescript
// Simple transfer - no side effects
const msg: MsgTransferTokens = {
    creator: 'bb1initiator...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1sender...',
            toAddresses: ['bb1recipient...'],
            balances: [
                {
                    amount: '1',
                    tokenIds: [{ start: '1', end: '1' }],
                    ownershipTimes: [
                        { start: '1', end: '18446744073709551615' },
                    ],
                },
            ],
            // No prioritizedApprovals - system auto-scans
        },
    ],
};
```

### Prioritized Transfer with Coin Transfers

```typescript
// Transfer with payment side effects - MUST prioritize
const msg: MsgTransferTokens = {
    creator: 'bb1initiator...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1sender...',
            toAddresses: ['bb1recipient...'],
            balances: [
                {
                    amount: '1',
                    tokenIds: [{ start: '1', end: '1' }],
                    ownershipTimes: [
                        { start: '1', end: '18446744073709551615' },
                    ],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'reward-approval',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: '1', // Must match current version
                },
            ],
        },
    ],
};
```

### Multiple Prioritized Approvals

```typescript
// Transfer using multiple approvals
const msg: MsgTransferTokens = {
    creator: 'bb1initiator...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1sender...',
            toAddresses: ['bb1recipient...'],
            balances: [
                {
                    amount: '1',
                    tokenIds: [{ start: '1', end: '1' }],
                    ownershipTimes: [
                        { start: '1', end: '18446744073709551615' },
                    ],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'collection-approval',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: '2',
                },
                {
                    approvalId: 'outgoing-approval',
                    approvalLevel: 'outgoing',
                    approverAddress: 'bb1sender...',
                    version: '0',
                },
            ],
        },
    ],
};
```

See [Auto-Scan Mode](../x-badges/concepts/auto-scan-mode.md) and [Transferability & Approvals](../x-badges/concepts/transferability-approvals.md) for detailed documentation.

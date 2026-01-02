# Defining and Locking Circulating Supply

This example demonstrates how circulating supply is dynamically calculated and how to control it through mint approval management.

## Overview

Unlike traditional blockchains with set-and-forget supply mechanisms, BitBadges supply is **dynamically calculated** based on the ability to use mint approvals and the ability to create new ones or edit them.

Thus, note that if the manager can create any new Mint approval, they can theoretically increase the supply by whatever the approval allows.

## Lock Supply Forever (Fixed Cap)

```typescript
const FullTimeRanges = [
    {
        start: '1',
        end: '18446744073709551615',
    },
];

const collectionPermissions = {
    // ... other permissions
    canUpdateCollectionApprovals: [
        {
            fromListId: 'Mint', // Target all mint approvals
            toListId: 'All',
            initiatedByListId: 'All',
            transferTimes: FullTimeRanges,
            tokenIds: FullTimeRanges,
            ownershipTimes: FullTimeRanges,
            approvalId: 'All',
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: FullTimeRanges, // Cannot update mint approvals
        },
    ],
};
```

**Result**: All Mint approvals are final. Whatever currently possible is possible but final.

## Controlled Supply (Managed Growth)

```typescript
const collectionPermissions = {
    // ... other permissions
    canUpdateCollectionApprovals: [
        {
            fromListId: 'Mint',
            toListId: 'All',
            initiatedByListId: 'All',
            transferTimes: FullTimeRanges,
            tokenIds: FullTimeRanges,
            ownershipTimes: FullTimeRanges,
            approvalId: 'initial-mint', // Only lock initial mint approval
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: FullTimeRanges,
        },
    ],
};
```

**Result**: "initial-mint" approval locked, but manager can add new ones.

## Dynamic Supply (Fully Flexible)

```typescript
const collectionPermissions = {
    // ... other permissions
    canUpdateCollectionApprovals: [], // Soft-enabled
    canAddMoreAliasPaths: [],
    canAddMoreCosmosCoinWrapperPaths: [],
};
```

**Result**: Manager can always modify mint approvals and adjust supply

## Lock Specific Token IDs

```typescript
const collectionPermissions = {
    // ... other permissions
    canUpdateCollectionApprovals: [
        {
            fromListId: 'Mint',
            toListId: 'All',
            initiatedByListId: 'All',
            transferTimes: FullTimeRanges,
            tokenIds: [
                {
                    start: '1',
                    end: '100',
                },
            ],
            ownershipTimes: FullTimeRanges,
            approvalId: 'All',
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: FullTimeRanges,
        },
    ],
};
```

**Result**: The Mint approvals for tokens 1-100 are locked and final. The manager can still create new Mint approvals for other token IDs or post-mint approvals for those tokens.

## Related Examples

-   [Freezing Mint Transferability](permissions/freezing-mint-transferability.md) - Lock all mint approvals
-   [Building Collection Approvals](building-collection-approvals.md) - Create mint approvals
-   [Empty Approval Criteria](empty-approval-criteria.md) - Unlimited mint template

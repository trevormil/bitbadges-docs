# Special Address Flags

Collection approval criteria include two boolean flags that control whether an approval can be used for **backed minting** and **special wrapping** operations.

## Overview

These flags provide fine-grained control over which collection approvals are eligible for special address operations, ensuring that only explicitly designated approvals can be used for backed minting and special wrapping transfers.

**Important:** These flags are only supported at the **collection approval level** (not user-level approvals).

## Interface

```typescript
interface ApprovalCriteria<T extends NumberType> {
    allowedBackedMinting?: boolean;
    allowSpecialWrapping?: boolean;
}
```

## Flags

### `allowedBackedMinting`

A boolean flag in `ApprovalCriteria` that determines whether this collection approval can be used for transfers involving **backed minting** (via `CosmosCoinBackedPath`).

**When `true`:**

-   The approval can be used for transfers to/from the backed path address
-   Allows the approval to participate in IBC token backing/unbacking operations

**When `false` (default):**

-   The approval cannot be used for backed minting operations
-   Transfers involving the backed path address will not match this approval

### `allowSpecialWrapping`

A boolean flag in `ApprovalCriteria` that determines whether this collection approval can be used for transfers involving **special wrapping** (via `CosmosCoinWrapperPath`).

**When `true`:**

-   The approval can be used for transfers to/from wrapper path addresses
-   Allows the approval to participate in cosmos coin wrapping/unwrapping operations

**When `false` (default):**

-   The approval cannot be used for special wrapping operations
-   Transfers involving wrapper path addresses will not match this approval

## Usage

These flags provide fine-grained control over which collection approvals are eligible for special address operations, ensuring that only explicitly designated approvals can be used for backed minting and special wrapping transfers.

### Example: Backed Minting Approval

```typescript
const backingApproval: CollectionApproval<bigint> = {
    fromListId: specialBackedPathAddress,
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 100n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'backing-approval',
    version: 0n,
    approvalCriteria: {
        allowedBackedMinting: true, // Required for IBC backed path operations
        overridesFromOutgoingApprovals: true,
    },
};
```

### Example: Wrapper Approval

```typescript
const wrapperApproval: CollectionApproval<bigint> = {
    toListId: specialWrapperAddress,
    fromListId: 'AllWithoutMint',
    initiatedByListId: 'All',
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 100n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'wrapper-approval',
    version: 0n,
    approvalCriteria: {
        allowSpecialWrapping: true, // Required for wrapper path operations
        overridesToIncomingApprovals: true,
    },
};
```

## Important Notes

-   These flags are **collection-level only** - they cannot be used in user-level (outgoing/incoming) approvals
-   Default value is `false` for both flags - approvals must explicitly opt-in to special address operations
-   These flags work in conjunction with other approval criteria - all criteria must be satisfied for the approval to match
-   When using these flags, you typically also need to use override flags (`overridesFromOutgoingApprovals` or `overridesToIncomingApprovals`) since special addresses cannot control their own user-level approvals


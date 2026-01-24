# Special Address Flags

Collection approval criteria include two boolean flags that control whether an approval can be used for **backed minting** and **special wrapping** operations.

## Overview

These flags provide fine-grained control over which collection approvals are eligible for special address operations, ensuring that only explicitly designated approvals can be used for backed minting and special wrapping transfers.

**Important:** These flags are only supported at the **collection approval level** (not user-level approvals).

## Interface

```typescript
interface ApprovalCriteria<T extends NumberType> {
    allowBackedMinting?: boolean;
    allowSpecialWrapping?: boolean;
}
```

## Flags

### `allowBackedMinting`

Controls whether a collection approval can be used for backed minting operations (via `CosmosCoinBackedPath`). Defaults to `false` if not set.

Prevents accidental allowances when `toListIds` is "All" - even if an approval matches all addresses, it won't apply to backing/unbacking operations unless explicitly set to `true`. Validation is bidirectional, checking both `from` and `to` addresses.

### `allowSpecialWrapping`

Controls whether a collection approval can be used for special wrapping operations (via `CosmosCoinWrapperPath`). Defaults to `false` if not set.

Prevents accidental allowances when `toListIds` is "All" - even if an approval matches all addresses, it won't apply to wrapping/unwrapping operations unless explicitly set to `true`. Validation is bidirectional, checking both `from` and `to` addresses.

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
        allowBackedMinting: true, // Required for IBC backed path operations
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

-   **Collection-level only:** These flags are only supported at the collection approval level (not user-level approvals)
-   **Default:** Both flags default to `false` - approvals must explicitly opt-in to special address operations
-   **Override flags:** When using these flags, you typically also need override flags (`overridesFromOutgoingApprovals` or `overridesToIncomingApprovals`) since special addresses cannot control their own user-level approvals


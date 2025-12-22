## Mint Address

The **Mint address** is a reserved address string (`"Mint"`) representing each collection's minting source. It has unlimited balances, and any transfer from the Mint address creates new tokens out of thin air. The Mint address cannot receive tokens, only send/mint them.

### Manager Controls Minting

The **manager** of the collection controls minting by setting and updating collection transferability rules (`collectionApprovals`) and the updatability of such rules via permissions (`collectionPermissions.canUpdateCollectionApprovals`). The manager can:

-   Create, edit, or remove mint approvals (approvals where `fromListId: 'Mint'`) that allow specific minting patterns, according to the updatability permissions set (`canUpdateCollectionApprovals`)
-   Set the updatability permissions to lock the future updatability of mint approvals (locking, enabling, soft-enabling, etc.). This is flexible and allows fine-grained patterns like locking for specific times, to specific addresses, etc.

**Important distinction:** Approvals define what transfers are **allowed**, but minting only occurs on **executed transfers**. Approvals are transferability rules. Transfers execute dependent on those rules. Permissions control the updatability of approvals.

```typescript
// Manager sets initial mint approval
const collection: TokenCollection<bigint> = {
    managerTimeline: [
        {
            manager: 'bb1...',
            timelineTimes: [{ start: 1n, end: 18446744073709551615n }],
        },
    ],
    collectionApprovals: [
        {
            fromListId: 'Mint',
            toListId: 'All',
            initiatedByListId: 'All',
            transferTimes: [{ start: 1n, end: 18446744073709551615n }],
            tokenIds: [{ start: 1n, end: 18446744073709551615n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            approvalId: 'mint-approval',
            version: 0n,
            // ... other fields
        },
    ],
    collectionPermissions: {
        canUpdateCollectionApprovals: [
            {
                fromListId: 'Mint',
                toListId: 'All',
                initiatedByListId: 'All',
                transferTimes: [{ start: 1n, end: 18446744073709551615n }],
                tokenIds: [{ start: 1n, end: 18446744073709551615n }],
                ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
                approvalId: 'All',
                permanentlyPermittedTimes: [],
                permanentlyForbiddenTimes: [
                    { start: 1n, end: 18446744073709551615n },
                ], // Locks it forever
            },
        ],
        // ... other permission fields
    },
};
```

**To lock minting:** Disable approval updates permanently for any Mint approval. The current approvals set will be frozen and cannot be updated.

### Common Approaches

Two common approaches for managing supply:

#### 1. Mint All at Genesis, Then Lock

Mint all tokens you need upon collection creation, then lock mint approval updates. Control future flow with post-mint transferability.

For example:

1. Create collection with Mint approvals
2. Mint all tokens to yourself you may ever need (via MsgTransferTokens)
3. Lock minting forever

**Result:** Fixed supply. All tokens minted. Control distribution via post-mint transferability rules.

#### 2. Keep Mint as "Escrow" for Future Mints

Keep the Mint address as an escrow for future mints. Control flow with approval updatability and current approvals. For example:

1. Create collection with Mint approvals
2. Edit Mint approvals over time (according to permissions) to enable future minting as needed

**Result:** More dynamic supply. Manager can update mint approvals to enable future minting as needed. Current approvals control immediate minting capabilities, but the manager can always update them to enable more minting in the future.

## Circulating Supply

Circulating supply is the cumulative total of all tokens transferred from the Mint address. It's dynamic, not static. This may be a slightly different concept than you're used to, but it's a powerful one because it allows for you to control the supply of your tokens dynamically and flexibly over time.

### How It Works

Design of your Mint approvals is crucial to the supply control of your collection. The circulating supply increases each time tokens are transferred from the Mint address to any other address.

### Supply Control via Approvals

Use approval criteria to control who can mint, when, and how much:

```typescript
// Supply control via approvals
const mintApproval: CollectionApproval<bigint> = {
    fromListId: 'Mint',
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 18446744073709551615n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'supply-control',
    version: 0n,
    // Approval criteria control who can mint, when, how much
    approvalCriteria: {
        maxNumTransfers: 1000n, // Limit number of mints
        overridesFromOutgoingApprovals: true, // Required for Mint address
        // ... other criteria
    },
};
```

### Importance of Updatability Permissions

Even if current approvals don't allow minting, if the manager can update approvals, there is effectively unlimited supply potential. They can simply update and create a new unlimited mint approval. Thus, it is crucial to control both the current approvals and the updatability permissions.

```typescript
// Lock mint approvals to cap supply
const collectionPermissions: CollectionPermissions<bigint> = {
    canUpdateCollectionApprovals: [
        {
            fromListId: 'Mint',
            toListId: 'All',
            initiatedByListId: 'All',
            transferTimes: [{ start: 1n, end: 18446744073709551615n }],
            tokenIds: [{ start: 1n, end: 18446744073709551615n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            approvalId: 'All',
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ], // Manager cannot update
        },
    ],
    // ... other permission fields
};
```

## Best Practices

### Never Mix Mint with Other Addresses

**CRITICAL**: When handling collection approvals, you do NOT want to handle the Mint address with other addresses. By default, lists with reserved aliases like "All" include the Mint address. You do not want to accidentally allow minting when you don't intend to.

For proper design, we highly recommend separating minting and post-mint approvals (`fromListId: '!Mint'` vs `fromListId: 'Mint'`).

```typescript
// ❌ BAD - Never do this
const badApproval: CollectionApproval<bigint> = {
    fromListId: 'All', // Mixing Mint with other addresses
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 18446744073709551615n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'bad-approval',
    version: 0n,
    // ... other fields
};

// ✅ GOOD - Separate mint and post-mint approvals
const mintApproval: CollectionApproval<bigint> = {
    fromListId: 'Mint', // Minting only
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 18446744073709551615n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'mint-only',
    version: 0n,
    // ... other fields
};

const postMintApproval: CollectionApproval<bigint> = {
    fromListId: '!Mint', // All addresses except Mint
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 18446744073709551615n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'post-mint',
    version: 0n,
    // ... other fields
};
```

**Rules:**

-   Use `"Mint"` for minting approvals only
-   Use `"!Mint"` or `"AllWithoutMint"` for post-mint approvals
-   Never use `"All"` for `fromListId` (it includes Mint)
-   Never mix `"Mint"` with other addresses in the same list

### Mint Approvals Must Override

Mint address approvals must always override its user-level approvals to properly work, since it cannot control its own user-level approvals itself.

```typescript
const mintApproval: CollectionApproval<bigint> = {
    fromListId: 'Mint',
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 18446744073709551615n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'mint-approval',
    version: 0n,
    approvalCriteria: {
        overridesFromOutgoingApprovals: true, // Required
        // ... other criteria
    },
};
```

## Mint vs Mint Escrow Address

Most use cases use `"Mint"` as the reserved address in approvals. For advanced cases needing escrows or payouts, you can use the `mintEscrowAddress` as a helper.

```typescript
// Mint Escrow Address (for advanced cases)
const mintEscrowAddress = generateAlias(
    'badges',
    getAliasDerivationKeysForCollection(collectionId)
);
```

The Mint Escrow Address is a generated `bb1` address that holds Cosmos native funds on behalf of the Mint address. It's used for coin transfers, payouts, and escrows. See [Mint Escrow Address](../x-badges/concepts/mint-escrow-address.md) for details.

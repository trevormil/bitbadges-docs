Transferability in BitBadges is controlled through a hierarchical approval system with three levels: collection, outgoing, and incoming.

## Three Transferability Levels

BitBadges supports three levels of transferability control:

| Level          | Controlled By  | Use Case                               |
| -------------- | -------------- | -------------------------------------- |
| **Collection** | Manager/Issuer | Global rules, freezability, compliance |
| **Outgoing**   | Sender         | Listings, delegation                   |
| **Incoming**   | Recipient      | Bids, access control                   |

Each transfer must satisfy collection-level AND (unless overridden) user-level approvals, while also having sufficient balances to transfer.

## Collection Approvals

Collection approvals define transferability rules for the entire collection on a global level. These rules apply to both minting and post-minting transfers. These let the issuer / manager control global compliance and transferability, such as freezability, revocation, and more.

**All transfers must satisfy the collection approvals.**

```typescript
interface CollectionApproval<T extends NumberType> {
    // Core Fields - Define Who? When? What?
    toListId: string; // Who can receive?
    fromListId: string; // Who can send?
    initiatedByListId: string; // Who can initiate?
    transferTimes: UintRange<T>[]; // When can transfer happen?
    tokenIds: UintRange<T>[]; // Which token IDs?
    ownershipTimes: UintRange<T>[]; // Which ownership times?
    approvalId: string; // Unique identifier
    version: T; // Version control (incremented on each update)

    // Optional Fields
    uri?: string; // Metadata link
    customData?: string; // Custom data
    approvalCriteria?: ApprovalCriteria<T>; // Additional restrictions
}
```

### Example Approval

```typescript
const mintApproval = {
    fromListId: 'Mint',
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [{ start: '1691931600000', end: '1723554000000' }],
    tokenIds: [{ start: '1', end: '100' }],
    ownershipTimes: [{ start: '1', end: '18446744073709551615' }],
    approvalId: 'mint-to-all',
    version: '0',
    approvalCriteria: {
        maxNumTransfers: '1000', // Only 1000 transfers allowed
        overridesFromOutgoingApprovals: true, // Required for Mint
    },
};
```

**Translation:** Allow anyone to claim tokens 1-100 from the Mint address between Aug 13, 2023 and Aug 13, 2024.

## Approval Criteria

Approval criteria add additional restrictions beyond basic approval matching. They are used to control who can transfer, when, how much, hwo often, and more.

```typescript
interface ApprovalCriteria<T extends NumberType> {
    maxNumTransfers?: T; // Limit number of transfers
    approvalAmounts?: ApprovalAmounts<T>; // Limit transfer amounts
    coinTransfers?: CoinTransfer<T>[]; // Automatic coin transfers
    merkleChallenges?: MerkleChallenge<T>[]; // Require merkle proofs
    mustOwnTokens?: MustOwnToken<T>[]; // Require owning specific tokens
    overridesFromOutgoingApprovals?: boolean; // Override sender approvals
    overridesToIncomingApprovals?: boolean; // Override recipient approvals
    // ... more fields
}
```

See [Approval Criteria](../x-badges/concepts/approval-criteria/README.md) for all available criteria.

## User-Level Approvals

Senders and recipients can configure user-level approvals that gate transfers. These follow the same structure as collection approvals (minus hardcoded sender/recipient logic respectively).

Sender approvals control who can send tokens on behalf of the user. Recipient approvals control who can send tokens to the user.

### Outgoing Approvals

Control who can send tokens on behalf of the user:

```typescript
const outgoingApproval = {
    // fromListId: 'bb1user...', // Locked to the user's address, not in interface
    toListId: 'bb1...', // Who can receive from this user
    initiatedByListId: 'bb1...', // Who can initiate the transfer
    transferTimes: FullTimeRanges,
    tokenIds: FullTimeRanges,
    ownershipTimes: FullTimeRanges,
    approvalId: 'my-listing',
    version: '0',
    approvalCriteria: { ... }
};
```

### Incoming Approvals

Control who can send tokens to the user:

```typescript
const incomingApproval = {
    // toListId: 'bb1user...', // Locked to the user's address, not in interface
    fromListId: 'bb1...', // Who can send to this user
    initiatedByListId: 'bb1...', // Who can initiate the transfer
    transferTimes: FullTimeRanges,
    tokenIds: FullTimeRanges,
    ownershipTimes: FullTimeRanges,
    approvalId: 'my-bids',
    version: '0',
    approvalCriteria: { ... }
};
```

## Auto-Approval Flags

We provide auto-approval flags to automatically approve transfers without requiring explicit approval matching. These flags are used for convenience and ease of use. They are only available on the user-level approvals interface.

```typescript
interface UserBalanceStore {
    autoApproveSelfInitiatedOutgoingTransfers: boolean;
    autoApproveSelfInitiatedIncomingTransfers: boolean;
    autoApproveAllIncomingTransfers: boolean;
}
```

### Auto-Approve Self-Initiated Outgoing Transfers

When `autoApproveSelfInitiatedOutgoingTransfers: true`, outgoing transfers initiated by the user are automatically approved without checking outgoing approvals.

**Use case:** Convenience for users who want to freely send tokens they own without managing outgoing approval configurations.

### Auto-Approve Self-Initiated Incoming Transfers

When `autoApproveSelfInitiatedIncomingTransfers: true`, incoming transfers initiated by the user are automatically approved without checking incoming approvals.

**Use case:** Convenience for users who want to receive tokens they request (e.g., claiming, requesting airdrops) without managing incoming approval configurations.

### Auto-Approve All Incoming Transfers

When `autoApproveAllIncomingTransfers: true`, **all** incoming transfers are automatically approved regardless of who initiates them.

**Use case:** Users who want to accept all incoming transfers without restrictions. Useful for open wallets or accounts that should receive tokens from anyone.

### Permission Control

Users can only update these flags according to their user permissions; however, you typically always leave these soft-enabled (empty array) for all because users should always have full control over their own auto-approval settings.

```typescript
const userPermissions = {
    canUpdateAutoApproveSelfInitiatedOutgoingTransfers: [], // Soft-enabled
    canUpdateAutoApproveSelfInitiatedIncomingTransfers: [], // Soft-enabled
    canUpdateAutoApproveAllIncomingTransfers: [], // Soft-enabled
};
```

## Override Behavior

Collection approvals can override user-level approvals for administrative controls like freezing, revocation, or forced transfers. This is done via the `approvalCriteria` field and only available on the collection approval criteria interface.

If set to true, we do NOT check the corresponding user-level approvals for the sender and/or recipient.

```typescript
const collectionApproval = {
    fromListId: '!Mint',
    toListId: 'All',
    approvalCriteria: {
        overridesFromOutgoingApprovals: true, // Skip sender approvals
        overridesToIncomingApprovals: true, // Skip recipient approvals
    },
};
```

### When Overrides Apply

**Outgoing overrides:** When `overridesFromOutgoingApprovals: true`, the collection approval bypasses the sender's outgoing approvals. This enables:

-   Freezing tokens (prevent transfers regardless of user settings)
-   Forced revocation (remove tokens from users)
-   Administrative transfers (manager-controlled actions)

**Incoming overrides:** When `overridesToIncomingApprovals: true`, the collection approval bypasses the recipient's incoming approvals. This enables:

-   Forced transfers (send tokens even if recipient blocks them)
-   Administrative actions (manager-controlled distributions)

**Important:** Overrides are powerful and should be used carefully. They allow executing transfers that would otherwise be blocked by user settings.

If you want to setup your collection without any overrides, you can simply set the `invariants.noForcefulPostMintTransfers` to true. This will prevent any collection approvals from ever using override flags.

## Transfer Validation Process

Each transfer must 1) have sufficient balances, 2) satisfy the collection approvals, and 3) satisfy the corresponding user-level approvals (unless overridden).

**Validation flow:**

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption>Transfer validation flow</figcaption></figure>

1. **Balance validation**: Sender has sufficient tokens
2. **Collection approval**: At least one collection approval passes
3. **User approvals**: Sender/recipient approvals pass (unless overridden)

## Break-Down Logic

The system can break down transfers and approvals into partial matches to make transfers succeed. It deducts as much as possible from each approval as it iterates.

For proper design, you should try to design your approvals such that they never have to match to more than one. However, if needed, we break down the transfer and approvals as fine-grained as we can to make it succeed.

See [Auto-Scan and Prioritized Approvals](./auto-scan-and-prioritized-approvals.md) for details on how the system selects approvals and how you can selectively prioritize approvals.

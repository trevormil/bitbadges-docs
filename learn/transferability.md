# Transferability

Transferability in BitBadges is controlled through a hierarchical approval system with three levels: collection, outgoing, and incoming.

## Three Transferability Levels

BitBadges supports three levels of transferability control:

<div style="overflow-x: auto;">

| Level          | Controlled By  | Approval Level | Approver Address | Stored On              | Msg                                    | Use Case                               |
| -------------- | -------------- | -------------- | ----------------- | ---------------------- | -------------------------------------- | -------------------------------------- |
| **Collection** | Manager/Issuer | collection     | ""                | TokenCollection.collectionApprovals      | MsgCreateCollection / MsgUpdateCollection | Global rules, freezability, compliance |
| **Outgoing**   | Sender         | outgoing       | bb1...            | UserBalanceStore.outgoingApprovals       | MsgUpdateUserApprovals                 | Listings, delegation                   |
| **Incoming**   | Recipient      | incoming       | bb1...            | UserBalanceStore.incomingApprovals       | MsgUpdateUserApprovals                 | Bids, access control                   |

</div>

Each transfer must satisfy collection-level AND (unless overridden) user-level approvals, while also having sufficient balances to transfer.

## Approval vs Permission vs Transfers

* Approvals define the rules for transfers on multiple levels.
* Transfers execute if the approval rules defined allow it and sufficient balances.
* Permissions can control the updatability of approvals - `canUpdateCollectionApprovals`

### Transfer Validation Process

Each transfer must 1) have sufficient balances, 2) satisfy the collection approvals, and 3) satisfy the corresponding user-level approvals (unless overridden).

**Validation flow:**

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Transfer validation flow</p></figcaption></figure>

## Collection Approvals

Collection approvals define transferability rules for the entire collection on a global level. These rules apply to both minting and post-minting transfers. These let the issuer / manager control global compliance and transferability, such as freezability, revocation, and more.

**All transfers must satisfy the collection approvals.**

**Important:** Approval IDs are unique identifiers and must not collide with other collection approvals.

```typescript
// Stored on TokenCollection.collectionApprovals[] (CollectionApproval<T>[])
interface CollectionApproval<T extends bigint> {
    // Core Fields - Define Who? When? What?
    toListId: string; // Who can receive?
    fromListId: string; // Who can send?
    initiatedByListId: string; // Who can initiate?
    transferTimes: UintRange<T>[]; // When can transfer happen?
    tokenIds: UintRange<T>[]; // Which token IDs?
    ownershipTimes: UintRange<T>[]; // Which ownership times?
    approvalId: string; // Unique identifier - must not collide with other approvals on the same level
    version: T; // Version control (incremented on each update)

    // Optional Fields
    uri?: string; // Metadata link
    customData?: string; // Custom data
    approvalCriteria?: ApprovalCriteria<T>; // Additional restrictions
}
```

### The Six Core Fields

Every approval defines **Who? When? What?** through these six core fields:

| Field               | Type                             | Purpose                                 | Example                                          |
| ------------------- | -------------------------------- | --------------------------------------- | ------------------------------------------------ |
| `toListId`          | Address List ID                  | Who can receive tokens                  | `"All"`, `"Mint"`, `"bb1..."`                    |
| `fromListId`        | Address List ID                  | Who can send tokens                     | `"Mint"`, `"!Mint"`                              |
| `initiatedByListId` | Address List ID                  | Who can initiate transfer               | `"All"`, `"bb1..."`                              |
| `transferTimes`     | UintRange\[] (UNIX Milliseconds) | When transfer can occur                 | `[{start: 1691931600000n, end: 1723554000000n}]` |
| `tokenIds`          | UintRange\[] (Token IDs)         | Which token IDs                         | `[{start: 1n, end: 100n}]`                       |
| `ownershipTimes`    | UintRange\[] (UNIX Milliseconds) | Which ownership times to be transferred | `[{start: 1n, end: 18446744073709551615n}]`      |

### Example Approval

```typescript
const mintApproval: CollectionApproval<bigint> = {
    fromListId: 'Mint',
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [{ start: 1691931600000n, end: 1723554000000n }],
    tokenIds: [{ start: 1n, end: 100n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'mint-to-all',
    version: 0n,
    approvalCriteria: {
        maxNumTransfers: {
            overallMaxNumTransfers: 1000n,
            perFromAddressMaxNumTransfers: 0n,
            perToAddressMaxNumTransfers: 0n,
            perInitiatedByAddressMaxNumTransfers: 1n,
            amountTrackerId: 'mint-to-all',
        },
        overridesFromOutgoingApprovals: true, // Required for Mint
        // ... other criteria
    },
};
```

**Translation:** Allow anyone to claim tokens 1-100 from the Mint address between Aug 13, 2023 and Aug 13, 2024.

### Approval Criteria

Approval criteria adds additional restrictions beyond basic approval matching. They are used to control who can transfer, when, how much, how often, and more. If criteria is not satisfied, the approval is not satisfied.

```typescript
interface ApprovalCriteria<T extends bigint> {
    // Transfer limits and amounts
    maxNumTransfers?: MaxNumTransfers<T>; // Limit number of transfers
    approvalAmounts?: ApprovalAmounts<T>; // Limit transfer amounts
    predeterminedBalances?: PredeterminedBalances<T>; // Exact balance requirements
    
    // Automatic actions
    coinTransfers?: CoinTransfer<T>[]; // Automatic coin transfers (BADGE or sdk.Coin)
    userRoyalties?: UserRoyalties<T>; // Percentage-based transfer fees
    
    // Challenge requirements
    merkleChallenges?: MerkleChallenge<T>[]; // Require merkle proofs
    mustOwnTokens?: MustOwnToken<T>[]; // Require owning specific tokens
    dynamicStoreChallenges?: DynamicStoreChallenge<T>[]; // On-chain numeric checks
    ethSignatureChallenges?: ETHSignatureChallenge<T>[]; // Ethereum signature requirements
    
    // Address relationship requirements
    requireToEqualsInitiatedBy?: boolean; // to == initiatedBy
    requireFromEqualsInitiatedBy?: boolean; // from == initiatedBy
    requireToDoesNotEqualInitiatedBy?: boolean; // to != initiatedBy
    requireFromDoesNotEqualInitiatedBy?: boolean; // from != initiatedBy
    
    // Address type checks
    senderChecks?: AddressChecks; // Address checks for sender
    recipientChecks?: AddressChecks; // Address checks for recipient
    initiatorChecks?: AddressChecks; // Address checks for initiator
    
    // Overrides (collection-level only)
    overridesFromOutgoingApprovals?: boolean; // Override sender approvals
    overridesToIncomingApprovals?: boolean; // Override recipient approvals
    
    // Time-based restrictions
    altTimeChecks?: AltTimeChecks; // Alternative time checks (offline hours/days)
    
    // Approval behavior
    autoDeletionOptions?: AutoDeletionOptions; // Auto-delete after use
    mustPrioritize?: boolean; // Require explicit prioritization
}
```

See [Approval Criteria](../token-standard/learn/approval-criteria/) for all available criteria.

## User-Level Approvals

Senders and recipients can configure user-level approvals that gate transfers. These follow the same structure as collection approvals (minus hardcoded sender/recipient logic respectively and no override logic). Sender approvals control who can send tokens on behalf of the user. Recipient approvals control who can send tokens to the user.

Stored on UserBalanceStore.outgoingApprovals[] (OutgoingApproval<T>[]) and UserBalanceStore.incomingApprovals[] (IncomingApproval<T>[]).

```typescript
interface UserBalanceStore<T extends bigint> {
    balances: Balance<T>[];
    outgoingApprovals: OutgoingApproval<T>[];
    incomingApprovals: IncomingApproval<T>[];
    autoApproveSelfInitiatedOutgoingTransfers: boolean;
    autoApproveSelfInitiatedIncomingTransfers: boolean;
    autoApproveAllIncomingTransfers: boolean;
    userPermissions: UserPermissions<T>;
    // ... other fields
}
```

#### Outgoing Approvals

Control who can send tokens on behalf of the user:

```typescript
const outgoingApproval: OutgoingApproval<bigint> = {
    // fromListId: 'bb1user...', // Locked to the user's address, not in interface
    toListId: 'bb1...', // Who can receive from this user
    initiatedByListId: 'bb1...', // Who can initiate the transfer
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 18446744073709551615n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'my-listing',
    version: 0n,
    approvalCriteria: {
        // ... criteria fields
    },
};
```

#### Incoming Approvals

Control who can send tokens to the user:

```typescript
const incomingApproval: IncomingApproval<bigint> = {
    // toListId: 'bb1user...', // Locked to the user's address, not in interface
    fromListId: 'bb1...', // Who can send to this user
    initiatedByListId: 'bb1...', // Who can initiate the transfer
    transferTimes: [{ start: 1n, end: 18446744073709551615n }],
    tokenIds: [{ start: 1n, end: 18446744073709551615n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'my-bids',
    version: 0n,
    approvalCriteria: {
        // ... criteria fields
    },
};
```

### User-Level Auto-Approval Flags

We provide auto-approval flags to automatically approve transfers without requiring explicit approval matching. These flags are used for convenience and ease of use for user-level approval handling. Typically, we recommend leaving all the auto-approval flags set to true. 

#### Auto-Approve Self-Initiated Outgoing Transfers

When `autoApproveSelfInitiatedOutgoingTransfers: true`, outgoing transfers initiated by the user are automatically approved without checking outgoing approvals.

**Use case:** Convenience for users who want to freely send tokens they own without managing outgoing approval configurations.

#### Auto-Approve Self-Initiated Incoming Transfers

When `autoApproveSelfInitiatedIncomingTransfers: true`, incoming transfers initiated by the user are automatically approved without checking incoming approvals.

**Use case:** Convenience for users who want to receive tokens they request (e.g., claiming, requesting airdrops) without managing incoming approval configurations.

#### Auto-Approve All Incoming Transfers

When `autoApproveAllIncomingTransfers: true`, **all** incoming transfers are automatically approved regardless of who initiates them.

**Use case:** Users who want to accept all incoming transfers without restrictions. Useful for open wallets or accounts that should receive tokens from anyone.

## Override Behavior

Collection approvals can override user-level approvals for administrative controls like freezing, revocation, or forced transfers. This is done via the `approvalCriteria` field and only available on the collection approval criteria interface.

If set to true, we do NOT check the corresponding user-level approvals for the sender and/or recipient.

```typescript
const collectionApproval: CollectionApproval<bigint> = {
    approvalCriteria: {
        overridesFromOutgoingApprovals: true, // Skip sender approvals
        overridesToIncomingApprovals: true, // Skip recipient approvals
        // ... other criteria
    },
    // ... other fields
};
```

#### Mint Address Overrides

Because the Mint address cannot control its own user-level approvals, it must always override the sender's outgoing approvals to properly work.

```typescript
const mintApproval: CollectionApproval<bigint> = {
    fromListId: 'Mint',
    // ... other fields
    approvalCriteria: {
        overridesFromOutgoingApprovals: true, // Required for Mint
        // ... other criteria
    },
};
```

#### When Overrides Apply

**Outgoing overrides:** When `overridesFromOutgoingApprovals: true`, the collection approval bypasses the sender's outgoing approvals. This enables:

* Freezing tokens (prevent transfers regardless of user settings)
* Forced revocation (remove tokens from users)
* Administrative transfers (manager-controlled actions)

**Incoming overrides:** When `overridesToIncomingApprovals: true`, the collection approval bypasses the recipient's incoming approvals. This enables:

* Forced transfers (send tokens even if recipient blocks them)
* Administrative actions (manager-controlled distributions)

**Important:** Overrides are powerful and should be used carefully. They allow executing transfers that would otherwise be blocked by user settings.

If you want to setup your collection without any overrides, you can simply set the `invariants.noForcefulPostMintTransfers` to true. This will prevent any collection approvals from ever using override flags.

## Break-Down Logic

The system can break down transfers and approvals into partial matches to make transfers succeed. It deducts as much as possible from each approval as it iterates.

For proper design, you should try to design your approvals such that they never have to match to more than one. However, if needed, we break down the transfer and approvals as fine-grained as we can to make it succeed.

See [Auto-Scan and Prioritized Approvals](auto-scan-and-prioritized-approvals.md) for details on how the system selects approvals and how you can selectively prioritize approvals.

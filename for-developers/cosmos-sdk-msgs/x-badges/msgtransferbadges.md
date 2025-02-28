# MsgTransferBadges

Transferring badges on-chain can be facilitated with [MsgTransferBadges](https://bitbadges.github.io/bitbadgesjs/classes/MsgTransferBadges.html).

```typescript
export interface MsgTransferBadges<T extends NumberType> {
    creator: string;
    collectionId: T;
    transfers: Transfer<T>[];
}

export interface Transfer<T extends NumberType> {
    from: string;
    toAddresses: string[];
    balances: Balance<T>[];
    precalculateBalancesFromApproval?: ApprovalIdentifierDetails;
    merkleProofs?: MerkleProof[];
    memo?: string;
    prioritizedApprovals?: ApprovalIdentifierDetails[];
    onlyCheckPrioritizedApprovals?: boolean;
}

export interface ApprovalIdentifierDetails {
    approvalId: string;
    approvalLevel: string; //"incoming", "outgoing", or "collection"
    approverAddress: string; //leave "" if collection
}

export interface MerklePathItem {
    aunt: string;
    onRight: boolean;
}

export interface MerkleProof {
    aunts: MerklePathItem[];
    leaf: string;
}
```

**To / From Addresses**

The to and from addresses must be valid BitBadges addresses. The from address can also be "Mint".

**Precalculations**

If you are targeting to use an approval which uses the **predeterminedBalances** field (see [Approvals](../../core-concepts/balances-transfers/approval-criteria/)), then you want to calculate the balances at execution time because they are possibly dynamic and can change between submit time and execution time. For example, if badge IDs increment by 1 every transfer, at signing time, the user does not know what badges they will receive because there could be transfers in between sign time and execution time.

If this is the case, you can use **precalculateBalancesFromApproval** to specify which approval to precalculate from, and the balances will be calculated at execution time to what the current predetermined values are, rather than what is defined in **balances.**

**Merkle Proofs**

If any approval that you are targeting has a Merkle challenge, you must provide a valid Merkle proof via the **merkleProofs.**

**Prioritized Approvals**

By default, we linearly scan approvals in the order they are defined on-chain. However, sometimes, you may want to prioritize one approval over the other.

You can specify which ones to check first via **prioritizedApprovals**. If **onlyCheckPrioritizedApprovals** is true, we will not check any approval not in the prioritized ones.

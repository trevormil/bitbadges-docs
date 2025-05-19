# MsgTransferBadges

Transferring badges on-chain can be facilitated with [MsgTransferBadges](https://bitbadges.github.io/bitbadgesjs/classes/MsgTransferBadges.html).

```typescript
export interface MsgTransferBadges<T extends NumberType> {
    creator: string;
    collectionId: T;
    transfers: Transfer<T>[];
}
```

**To / From Addresses**

The to and from addresses must be valid BitBadges addresses. The from address can also be "Mint".

**Precalculations**

If you are targeting to use an approval which uses the **predeterminedBalances** field (see [Approvals](../../../badges-advanced/balances-transfers/approval-criteria/)), then you want to calculate the balances at execution time because they are possibly dynamic and can change between submit time and execution time. For example, if badge IDs increment by 1 every transfer, at signing time, the user does not know what badges they will receive because there could be transfers in between sign time and execution time.

If this is the case, you can use **precalculateBalancesFromApproval** to specify which approval to precalculate from, and the balances will be calculated at execution time to what the current predetermined values are, rather than what is defined in **balances.**

**Merkle Proofs**

If any approval that you are targeting has a Merkle challenge, you must provide a valid Merkle proof via the **merkleProofs.**

**Prioritized Approvals**

All approvals with custom approval criteria (coinTransfers, approvalCriteria) must be prioritized. If it is just an open-ended approval with no restrictions beyond sender, recipient, initiatedBy, badgeIds, ownershipTimes, transferTimes, etc, then it does not need to be prioritized.

Our algorithm:

1. Check all prioritized approvals first in the order they were specified
2. Check all unchecked approvals WITHOUT side-effects in the order they are defined on-chain.&#x20;

If **onlyCheckPrioritizedApprovals** is true, we will not check any approval not in the prioritized ones.

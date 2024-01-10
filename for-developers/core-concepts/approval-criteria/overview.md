# Overview

In the transferability page, we mainly talked about how we match and check an approval. Here, we will talk about the **approvalCriteria.**

These are the additional options or restrictions you can set which decide whether a transfer is approved or not (e.g. how much can be transferred? how many times? etc). To be approved, it must satisfy all the options / restrictions set (everything talked about in the last page AND the **approvalCriteria**).

Note these are just the native options provided for convenience and consistency. You can implement your own approvals via custom logic with a CosmWASM smart contract.

```typescript
export interface ApprovalCriteria<T extends NumberType> {
  //any badges the initiator must own? KYC badge? verified badge?
  mustOwnBadges?: MustOwnBadges<T>[];
  //does the initiator have to provide a valid Merkle path to this challenge to be approved?
  merkleChallenge?: MerkleChallenge<T>;
  
  //How many badges can be transferred? which badges? In how many transfers?
  predeterminedBalances?: PredeterminedBalances<T>;
  approvalAmounts?: ApprovalAmounts<T>;
  maxNumTransfers?: MaxNumTransfers<T>;

  //incoming only has the "from" fields
  //outgoing only has the "to" fields
  requireToEqualsInitiatedBy?: boolean; 
  requireFromEqualsInitiatedBy?: boolean;
  requireToDoesNotEqualInitiatedBy?: boolean;
  requireFromDoesNotEqualInitiatedBy?: boolean;

  //only for collection level
  overridesFromOutgoingApprovals?: boolean;
  overridesToIncomingApprovals?: boolean;
}
```

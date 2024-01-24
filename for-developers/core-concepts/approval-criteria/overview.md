# Overview

In the transferability page, we mainly talked about how we match and check an approval. Here, we will talk about the **approvalCriteria.**

These are the additional options or restrictions you can set which decide whether a transfer is approved or not (e.g. how much can be transferred? how many times? etc). To be approved, it must satisfy all the options / restrictions set (everything talked about in the last page AND the **approvalCriteria**).

Note these are just the native options provided in the interface for convenience and consistency, but you can always implement your own approvals via custom logic with a CosmWASM smart contract.

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

#### Tracker IDs

The approval interface utilizes trackers behind the scenes which are identified by IDs. Trackers are also increment only and immutable in storage. Since they are ID based, multiple approvals can use the same tracker ID to implement cross-approval logic. However, the typical expected behavior is keeping it scoped which is explained below.

**Best Practices - Creating / Updating / Deleting**

A really important part in creating / editing / deleting approvals is to keep track of the trackers. You do not want to use a tracker with prior history when creating an approval which should start from scratch. This messes up the expected behavior of the approval. You can't just simply delete the tracker because it is increment only and immutable.

To combat this, we recommend the following best practices:

* When creating an approval for the first time (which is expected to start completely from scratch), always use unique, unused IDs for **approvalId**, **amountTrackerId** and **challengeTrackerId**. If these are unique, the tracker IDs will also be unique which means they will have no prior history.
* Updating an existing approval is tricky. If you can, we recommend deleting the existing approval + creating a new one that starts from scratch. This takes all the headaches away from verifying tracker history is as intended and keeping behavior as expected. If you cannot, please be mindful of how trackers work behind the scenes and what any potential updates may do to the expected functionality.

#### Scoped vs Cross-Approval Logic

Typically, approvals are expected to be scoped, but sometimes, you may want to implement cross-approval logic (do not allow double dipping between two approvals). We explain this in the Advanced page at the end of this section.

For keeping it scoped, all three of **approvalId**, **amountTrackerId** and **challengeTrackerId** (this is on the parent interface, not within criteria) should be the same. If you keep them the same, all logic is always scoped to the current approval, and nothing any other approval can do can affect the current approval's behavior.

Behind the scenes, being scoped is enforced because we restrict that the **amountTrackerId** and **challengeTrackerId** cannot be the **approvalId** of another approval. Thus, if **amountTrackerId = challengeTrackerId = approvalId,** you know that other approvals cannot use the same tracker IDs and mess up the current approval.&#x20;

```json
{
    ...
    "approvalId": "abc123",
    "amountTrackerId": "abc123",
    "challengeTrackerId": "abc123"
    ...
}
```


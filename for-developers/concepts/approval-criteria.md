# ✅ Approval Criteria

In the last page, we mainly talked about how we match and check an approved transfer. Here, we will talk about the **approvalCriteria.**&#x20;

These are the additional options or restrictions you can set which decide whether a transfer is approved or not (e.g. how much can be transferred? how many times? etc). To be approved, it must satisfy all the options / restrictions set.&#x20;

Note these are just the native options provided for convenience and consistency. You can implement your own approvals via custom logic with a smart contract.

```typescript
export interface ApprovalCriteria<T extends NumberType> {
  //any badges the initiator must own? KYC badge? verified badge?
  mustOwnBadges?: MustOwnBadges<T>[];
  //does the initiator have to provide a valid Merkle path to this challenge?
  merkleChallenge?: MerkleChallenge<T>;
  
  //How many badges can be transferred? In how many transfers?
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

### **Overrides**

As mentioned previously, the collection-wide approvals can override the user-level approvals. This is done via the **overridesFromOutgoingApprovals** or **overridesToIncomingApprovals**.&#x20;

If set to true, we will not check the user's incoming / outgoing approvals respectively. Essentially, it is forcefully transferred without needing user approvals.

IMPORTANT: The Mint address has its own approvals store, but since it is not a real address, they are always empty. Thus, it is important that when you attempt transfers from the Mint address, you override the outgoing approvals of the Mint address.

* <pre class="language-json"><code class="lang-json"><strong>"fromMappingId": "Mint", //represents the mapping with the "Mint" addres
  </strong>...
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true
    ...
  }
  </code></pre>

### **Must Own Badges**

Must own badges are another unique feature that is very powerful. This allows you to specify certain badges and amounts of badges of another collection that must be owned at custom times in order to be approved. &#x20;

For example, you may implement a badge collection where only holders of a verified badge are approved to send and receive badges. Or, you may implement what you must NOT own a scammer badge in order to interact.

Note that collections with "Off-Chain" balances are not supported for this feature.

```typescript
export interface MustOwnBadges<T extends NumberType> {
  collectionId: T;

  amountRange: UintRange<T>; //min/max amount to be owned
  ownershipTimes: UintRange<T>[];
  badgeIds: UintRange<T>[];

  overrideWithCurrentTime: boolean; //override ownershipTimes with time of block at execution
  
  //if true, must own all badges at all times specified
  //if false, must meet the criteria for at least one badge and at least one ownership time (one millisecond)
  mustOwnAll: boolean; 
}
```

### **Tracker IDs**

```typescript
export interface CollectionApproval<T extends NumberType> {
  approvalTrackerId: string;
  challengeTrackerId: string;
  ...
}
```

On the previous page, we explained the approval interface. This interface had two fields: **challengeTrackerId** and **approvalTrackerId**. These are string identifiers which we use to track and tally approvals and what has been processed vs not yet (if applicable).

**What are trackers?**

We use trackers and tallies interchangably throughout this documentation. Approvals trackers and challenge trackers are two separate concepts.

**Approvals trackers** track how many badges have been transferred and how many transfers occur. The identifier of each approval tracker consists of **approvalTrackerId** along with other identifying details.

**Challenge trackers** track which leaves were used in a Merkle challenge (the approvee needs to provide a valid Merkle path). The identifier for each challenge tracker consists of **challengeTrackerId** along with other identifying details.

**How do trackers work?**

BitBadges tracks approvals and used challenges using an incrementing tally system with a threshold. For example,

1. You are approved for x10 of badge IDs 1-10
2. You transfer x5 of badge IDs 1-10 -> tally goes from x0/10 -> x5/10
3. You transfer another x5 -> tally goes to x10/10
4. You transfer another x1 -> exceed threshold and transfer fails

**Handling Multiple Trackers**

Multiple tallies can be created and are each identified by a unique ID. Tallies are increment only and immutable in storage. To start an approval tally from scratch, you will need to map the approval to a new unused ID. This can be done by editing **approvalTrackerId** or **challengeTrackerId.**

It is recommended that each approval on a level uses unique unused tracker IDs for approval trackers and/or challenge trackers, but for advanced functionality (see end of page), they can be linked using the same IDs.

**As-Needed Basis**

We calculate tallys on an as-needed basis. Meaning, if there is no need to increment the tally (unlimited limit and/or not restrictions), we do not increment. Take this into consideration, especially if you are changing IDs.&#x20;

For example, changing from an unlimited restriction to some restriction is not retroactive and is only tallied for transfers while it has a restriction. Likewise, be mindful of previously used IDs because they are non-deletable and increment-only.

### Approval Trackers

Approval trackers are tallies that track how much of badges have been transferred and how many transfers take place. Each transfer increments **numTransfers** and each badge transferred increments the **amounts** in the interface below.

```typescript
export interface ApprovalsTrackerInfoBase<T extends NumberType> extends ApprovalTrackerIdDetails<T> {
  numTransfers: T;
  amounts: Balance<T>[];
}
```

Each individual tracker has a unique ID made up of six fields.

```
ID: collectionId-approvalLevel-approverAddress-approvalTrackerId-trackerType-approvedAddress
```

```typescript
export interface ApprovalTrackerIdDetails<T extends NumberType> {
  collectionId: T
  approvalLevel: "collection" | "incoming" | "outgoing" | ""
  approverAddress: string
  approvalTrackerId: string
  trackerType: "overall" | "to" | "from" | "initiatedBy" | ""
  approvedAddress: string
}
```

**approvalLevel** corresponds to whether it is a collection-level approval, user incoming approval, or useroutgoing approval. If it is user level, the **approverAddress** is the user setting the approval. **approverAddress** is blank for collection level.

The **approvalTrackerId** is defined by the approval itself (see previous page).&#x20;

The trackerType corresponds to what type of tracker it is. If "overall", this is applicable to any address and will increment EVERY time this approval is used. If "to", "from", or "initiatedBy", it will only increment when the **approvedAddress** is the sender, recipient, or initiator of the transfer.



Moving forward, let's say we are dealing with the collection approvals approving transfers from Bob  for collection 1, so `1-collection-bob-uniqueID-?-?`

<pre class="language-typescript"><code class="lang-typescript"><strong>{
</strong>  collectionId: 1
  approvalLevel: "collection"
  approverAddress: bob
  approvalTrackerId: "some unique id"
  trackerType: ?
  approvedAddress: ?
}
</code></pre>

#### **Approval Amount Tallies**

Approval amounts (**approvalAmounts**) allow you to specify the threshold amount that can be transferred for this approval. This is similar to other interfaces (such as approvals for ERC721).&#x20;

If nil value or "0", we assume there is no limit (no amount restrictions).

We define four levels that you can specify for approval amounts as seen below. You can define multiple if desired. To be approved, the transfer must satisfy all.

* **Overall**: Overall will increment the cumulative tally for all transfers that match this approval, regardless of who sends, receives, or initiates them.
* **Per To Address**: If you specify an approval amount per to address, we will create unique cumulative tallies for every unique "to" address.&#x20;
  * Ex: Each unique address can only receive x1 of badge ID 1
* **Per From Address**: Creates unique cumulative tallies for every unique "from" address.&#x20;
* **Per Initiated By Address**: Creates unique cumulative tallies for every unique "initiatedBy" address.&#x20;

```json
"approvalAmounts": {
    "overallApprovalAmount": "1000", 
    "perFromAddressApprovalAmount": "0", //no limit
    "perToAddressApprovalAmount": "0",
    "perInitiatedByAddressApprovalAmount": "10" //limit of x10
},
```

The amounts approved are limited to the **badgeIds** and **ownershipTimes** defined by the approval (see previous page).



So let's say we have the **approvalAmounts** defined above and Alice initiates a transfer of x10 from Bob. There are two separate trackers that get incremented here.

\#1) Tracker with the following ID `1-collection-bob-uniqueID-overall-` gets incremented to x10 out of 1000. Any subsequent transfers (from any address) will increment this overall approval amount as well.

\#2) Tracker with ID `1-collection-bob-uniqueID-initiatedBy-alice`gets incremented to x10 out of 10 used. Alice has now fully used up her threshold for this tracker.



Since there was an unlimited amount approved for "to" and "from" trackers, we do not increment anything (as-needed basis).

### **Max Number of Transfers**

Similar to approval amounts, you can also specify the maximum number of transfers that can occur.  These will be incremented by 1 each transfer.&#x20;

<pre class="language-json"><code class="lang-json">"maxNumTransfers": {
<strong>    "overallMaxNumTransfers": "0",
</strong>    "perFromAddressMaxNumTransfers": "0",
    "perToAddressMaxNumTransfers": "0",
    "perInitiatedByAddressMaxNumTransfers": "1"
},
</code></pre>

So if we reuse the previous example where Alice initiates a transfer, the tracker `1-collection-bob-uniqueID-initiatedBy-alice`would increment to 1/1 transfers used. Alice can no longer initiate another transfer.

Again, nothing is incremented unnecessarily, so "overall", "to", and "from" trackers are not incremented.

Note that sometimes, in [Predetermined Balances](approval-criteria.md#predetermined-balances), if you specify useOverallNumTransfers, usePerToAddressNumTransfers, usePerFromAddressNumTransfers, or usePerInitiatedByAddressNumTransfers, we do need the number of transfers for the corresponding tracker and do increment (even if the corresponding maximum number of transfers for that level is not set / set to no limit).

### **Predetermined Balances**

Predetermined balances are a new way of having fine-grained control over the amounts that are approved with each transfer. In a tally-based system where you approve X amount to be transferred, you have no control over the combination of amounts that will add up to X. For example, if you approve x100, you can't control whether the transfers are \[x1, x1, x98] or \[x100] or another combination.

Predetermined balances let you explicitly define the amounts that must be transferred and the order of the transfers. For example, you can enforce x1 of badge ID 1 has to be transferred before x1 of badge ID 2, and so on.&#x20;

**Pretty much, the transfer will fail if the balances are not EXACTLY as defined in the predetermined balances.**

```typescript
export interface PredeterminedBalances<T extends NumberType> {
  manualBalances: ManualBalances<T>[];
  incrementedBalances: IncrementedBalances<T>;
  orderCalculationMethod: PredeterminedOrderCalculationMethod;
}
```

**Defining Balances**

There are two ways to define the balances. Both can not be used together.

* **Manual Balances:** Simply define an array of balances manually. Each element corresponds to a different set of balances for each unique transfer.
* ```json
  "manualBalances": [
    {
      "amount": "1",
      "badgeIds": [
        {
          "start": "1",
          "end": "1"
        }
      ],
      "ownershipTimes": [
        {
          "start": "1691978400000",
          "end": "1723514400000"
        }
      ]
    },
    {...},
    {...},
  ]
  ```
* **Incremented Balances:** Define starting balances and then define how much to increment the IDs and times by after each transfer. This is how to implement the above example: you can enforce x1 of badge ID 1 has to be transferred before x1 of badge ID 2, and so on.
* ```json
  "incrementedBalances": {
    "startBalances": [
      {
        "amount": "1",
        "badgeIds": [
          {
            "start": "1",
            "end": "1"
          }
        ],
        "ownershipTimes": [
          {
            "start": "1691978400000",
            "end": "1723514400000"
          }
        ]
      }
    ],
    "incrementBadgeIdsBy": "1",
    "incrementOwnershipTimesBy": "0"
  }
  ```

**Defining Order of Transfers**

The order is calculated by a specified order calculation method.&#x20;

For manual balances, we want to determined which element index of the array is transferred (e.g. order number = 0 means the balances of manualBalances\[0] will be transferred). For incremented balances, this corresponds to how many times we should increment (e.g. order number = 5 means apply the increments to the starting balances five times).

There are five calculation methods to determine the order method. We either use a running tally of the number of transfers to calculate the order number (no previous transfers = order number 0, one previous = order number 1, and so on). This can be done on an overall or per to/from/initiatedBy address basis and is incremented as explained in [Max Number of Transfers](approval-criteria.md#max-number-of-transfers). Note this increments the same tracker. So even if you have unlimited number of transfers defined in **maxNumTransfers,** this will increment it.

We also support using the leaf index for the defined Merkle challenge proof (see [Merkle Challenges](approval-criteria.md#merkle-challenges) below) to calculate the order number (e.g. leftmost leaf will correspond to order number 0, next leaf will be order number 1, and so on). The leftmost leaf means the leftmost leaf of the **expectedProofLength** layer.

This is used to reserve specific badges for specific users / claim codes. For example, reserve the badges corresponding to order number 10 (leaf number 10) for address xyz.eth.

```typescript
export interface PredeterminedOrderCalculationMethod {
  useOverallNumTransfers: boolean;
  usePerToAddressNumTransfers: boolean;
  usePerFromAddressNumTransfers: boolean;
  usePerInitiatedByAddressNumTransfers: boolean;
  useMerkleChallengeLeafIndex: boolean;
}
```

Although this can be used in tandem with approval amounts, either one or the other is usually used because they both specify amount restrictions.

### **Merkle Challenges**

Merkle challenges allow you to define a SHA256 Merkle tree, and to be approved for each transfer, the initiator of the transfer must provide a valid Merkle path for the tree via **merkleProofs** in MsgTransferBadges.&#x20;

For example, you can create a Merkle tree of claim codes. Then to be able to claim badges, each claimee must provide a valid Merkle path from the claim code to the **root**. You distribute the codes (and paths) in any method you prefr.

The **expectedProofLength** defines the expected length for the Merkle proofs to be provided. This is to avoid preimage and second preimage attacks. **All proofs must be of the correct length, which means you must design your trees accordingly.**&#x20;

We also support a couple customization options. First, you can set **useCreatorAddressAsLeaf**. If set, we will override the provided leaf of each Merkle proof with the msg.creator aka the Cosmos address of the initiator of the transaction. This can be used to implement whitelist trees. Note that the initiator must also be within the initiatedByMapping of the approval for it to make sense.

The **maxOneUsePerLeaf** is important to be set if you want to prevent against replay attacks. For example, ensure that a code / proof can only be used once.

This is stored in a challenge tracker, similar to the approvals trackers explained above. Each is identified by an ID made up of the following fields:

```typescript
{
  collectionId: T;
  approvalLevel: "collection" | "incoming" | "outgoing";
  approverAddress?: string;
  challengeTrackerId: string;
  leafIndex: T;
}
```

**challengeTrackerId** is defined by the approval (see previous page). **leafIndex** is the index of the leaf that we are tracking (0 = leftmost and so on).

We simply track if the leaf has been used and only allow it to be used once if **maxOneUsePerLeaf** is specified. Like approval trackers, this is increment only and non-deletable.

```typescript
export interface MerkleChallenge<T extends NumberType> {
  root: string
  expectedProofLength: T;
  useCreatorAddressAsLeaf: boolean
  maxOneUsePerLeaf: boolean
  uri: string
  customData: string
}
```

<pre class="language-json"><code class="lang-json"><strong> "merkleChallenge": {
</strong>   "root": "758691e922381c4327646a86e44dddf8a2e060f9f5559022638cc7fa94c55b77",
   "expectedProofLength": "1",
   "useCreatorAddressAsLeaf": true,
   "maxOneUsePerLeaf": true,
   "uri": "ipfs://Qmbbe75FaJyTHn7W5q8EaePEZ9M3J5Rj3KGNfApSfJtYyD",
   "customData": ""
}
</code></pre>

### **Requires**

You also have the following options to further restrict who can transfer to who. Note that this is only applicable to the addresses that match in the respective mappings for to, from, and initiatedBy.

**requireToEqualsInitiatedBy, requireToDoesNotEqualsInitiatedBy**

**requireFromEqualsInitiatedBy, requireFromDoesNotEqualsInitiatedBy**

These are pretty self-explanatory. You can enforce that we additionally check if the to or from address equals or does not equal the initiator of the transfer.

### Advanced: Linking Tracker Ids

In some instances, you may want to link tracker IDs. For example, create approval #1 which is public and allows anyone to claim x10 as well as approval #2 which has stricter criteria but allows you to claim x1000. However, you do not want any double dipping and someone potentially claiming x1010.

There are a couple ways you can handle it. You can design in a away that approves x10 and x990, or you can link the approvals via the tracker IDs. Multiple approvals on the same level can have the same tracker ID if **approvalTrackerId** and all other identifying details are the same. This allows you to increment both trackers when one approval or the other is matched to.

**Advanced: Linking Approvals**

You can link approvals using the challenge and tallying ID mechanisms. As explained above, we keep track of approvals and used leaves using an incrementing tally system where each tally is identified by an ID.&#x20;

These IDs are not limited to a single approval and can be used multiple times across a level. If multiple approvals / challenges have the same ID, the tally corresponding to that ID will be incremented whenever ANY of the approvals / challenges are satisfied. Thus, it increases the tally for ALL the approvals / challenges.

**Example 1:**

For example, the following pseudocode for approvalDetails would allow address C (in both whitelist ABC and CDE) to claim a max of x10 because the tallys are linked (same tracker ID).

```json
[{
    Tracker ID: 123
    Approve x5 of IDs 1-10 if you are on whitelist ABC,
}, {
    Tracker ID: 123,
    Approve x10 of IDs 1-10 if you are on whitelist CDE
}]
```

But, the following would allow address C to claim a max of x15 because the tally for x10 and x5 are not linked.

```json
[{
    Tracker ID: 123
    Approve x5 of IDs 1-10 if you are on whitelist ABC,
}, {
    Tracker ID: 456,
    Approve x10 of IDs 1-10 if you are on whitelist CDE
}]
```

**Example 2:** Lets say you want to create an approval where a user must either own badge ID 1 from collection 1 or own badge ID 1 from collection 2 but they cannot own both (so XOR logic). You could do something like this:

```json
[{
    Tally ID: 123
    Approve x5 of IDs 1-10 if:
        A) you own >1 of badge ID 1 from collection 1 
        B) you own x0 of badge ID 1 from collection 2
}, {
    Tally ID: 123,
    Approve x5 of IDs 1-10 if:
        A) you own >1 of badge ID 1 from collection 2 
        B) you own x0 of badge ID 1 from collection 1
}]
```

Even though these are two separate approvals, they are inherently linked via the tally ID.

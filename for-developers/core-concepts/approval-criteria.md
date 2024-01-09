# ✅ Approval Criteria

In the last page, we mainly talked about how we match and check an approved transfer. Here, we will talk about the **approvalCriteria.**

These are the additional options or restrictions you can set which decide whether a transfer is approved or not (e.g. how much can be transferred? how many times? etc). To be approved, it must satisfy all the options / restrictions set.

Note these are just the native options provided for convenience and consistency. You can implement your own approvals via custom logic with a smart contract.

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

### **Overrides**

As mentioned in the previous page, the collection-wide approvals can override the user-level approvals. This is done via **overridesFromOutgoingApprovals** or **overridesToIncomingApprovals**.

If set to true, we will not check the user's incoming / outgoing approvals for the approved balances respectively. Essentially, it is forcefully transferred without needing user approvals.

This can be leveraged to implement forcefully revoking a badge.

IMPORTANT: The Mint address has its own approvals store, but since it is not a real address, they are always empty. Thus, it is important that when you attempt transfers from the Mint address, you override the outgoing approvals of the Mint address.

* <pre class="language-json"><code class="lang-json"><strong>"fromListId": "Mint", //represents the list with the "Mint" addres
  </strong>...
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true
    ...
  }
  </code></pre>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### **Must Own Badges**

Must own badges are another unique feature that is very powerful. This allows you to specify certain badges and amounts of badges of a collection (typically a different collection) that must be owned at custom times in order to be approved.

For example, you may implement a badge collection where only holders of a verified badge are approved to send and receive badges. Or, you may implement what you must NOT own (own x0) a scammer badge in order to interact.

Note that collections with "Off-Chain" balances are not supported for this feature.

```typescript
export interface MustOwnBadges<T extends NumberType> {
  collectionId: T;

  amountRange: UintRange<T>; //min/max amount expected to be owned
  ownershipTimes: UintRange<T>[];
  badgeIds: UintRange<T>[];
  
  //override ownershipTimes with the exact block millisecond at execution
  //Ex: [{start: 12345, end: 12345}]
  overrideWithCurrentTime: boolean;
  
  //if true, must own ALL badges at ALL times specified
  //if false, must own at least one specified badge for at least one specified millisecond
  mustOwnAll: boolean; 
}
```

### **Trackers**

```typescript
export interface CollectionApproval<T extends NumberType> {
  amountTrackerId: string;
  challengeTrackerId: string;
  ...
}
```

On the previous page, we explained the approval interface. This interface had two fields: **challengeTrackerId** and **amountTrackerId**. These are string identifiers which we use to track and tally approvals and what has been processed / used up vs not yet (if applicable).

**What are trackers?**

Approvals trackers track how many badges have been transferred and how many transfers occur.

Challenge trackers track which leaves were used in a Merkle challenge (the approvee needs to provide a valid Merkle path for approval).

Approvals trackers and challenge trackers are two separate concepts but follow the same logic pretty much. We use the terms trackers and tallies interchangeably throughout this documentation.

**How are they identified?**

The identifier of each approval tracker consists of **amountTrackerId** along with other identifying details.

```
ID: collectionId-approvalLevel-approverAddress-amountTrackerId-trackerType-approvedAddress
```

```typescript
export interface ApprovalTrackerIdDetails<T extends NumberType> {
  collectionId: T
  approvalLevel: "collection" | "incoming" | "outgoing" | ""
  approverAddress: string
  amountTrackerId: string
  trackerType: "overall" | "to" | "from" | "initiatedBy" | ""
  approvedAddress: string
}
```

The identifier for each challenge tracker consists of **challengeTrackerId** along with other identifying details.

```typescript
{
  collectionId: T;
  approvalLevel: "collection" | "incoming" | "outgoing";
  approverAddress?: string;
  challengeTrackerId: string;
  leafIndex: T;
}
```

**approvalLevel** corresponds to whether it is a collection-level approval, user incoming approval, or useroutgoing approval. If it is user level, the **approverAddress** is the user setting the approval. **approverAddress** is blank for collection level.

**How do trackers work?**

BitBadges tracks approvals and used challenges using an incrementing tally system with a threshold. Take the following approval tracker

1. You are approved for x10 of badge IDs 1-10. The tracker ID maps to "xyz".
2. You transfer x5 of badge IDs 1-10 -> "xyz" tally goes from x0/10 -> x5/10
3. You transfer another x5 -> "xyz" tally goes to x10/10
4. You transfer another x1 -> exceed threshold and transfer fails for all subsequent approvals that match to "xyz".
5. If the tracker ID changes to "abc", the tracker is treated as new and tallies start from zero.

This is similarly applied to challenges, except we track if a certain leaf index has been used or not.

**As-Needed Basis**

We increment tallies on an as-needed basis. Meaning, if there is no need to increment the tally (unlimited limit and/or not restrictions), we do not increment for efficiency purposes.

**Handling Multiple Trackers**

Trackers are ID-based, and multiple trackers can be created. Take note of what makes up the ID. The collection ID, approval level, approver address, and more are all considered. If one changes or is different, the whole ID is different and will correspond to a new tracker.

Trackers are increment only and immutable in storage. To start an approval tally from scratch, you will need to map the approval to a new unused ID. This can be done simply by editing **amountTrackerId** or **challengeTrackerId** (because this changes the whole ID) or restructuring to change one of the other fields that make up the overall ID.

Because of the immutable nature, be careful to not revert to a previously used ID unintentionally because the starting point will be the previous tally (not starting from scratch).

**Linking Tracker IDs**

In some instances, you may want to link tracker IDs. It is typically recommended for simplicity that each approval / challenge corresponds to a unique tracker ID, but for advanced functionality, multiple can be linked using the same IDs. This can allow you to implement OR or XOR logic between approvals / challenges.

For example, you do not want any double dipping into two different approvals. Multiple approvals on the same level can map to the same tracker ID if **amountTrackerId** and all other identifying details are the same. This allows you to increment the same tracker for both approvals.The tracker will be incremented whenever ANY of the approvals / challenges are satisfied.

**Example 1:**

For example, the following pseudocode would allow address C (in both allowlist ABC and CDE) to claim a max of x10 because the tallys are linked (same tracker ID).

```json
[{
    Tracker ID: 123
    Approve x5 of IDs 1-10 if you are on allowlist ABC,
}, {
    Tracker ID: 123,
    Approve x10 of IDs 1-10 if you are on allowlist CDE
}]
```

But, the following would allow address C to claim a max of x15 because the tally for x10 and x5 are not linked.

```json
[{
    Tracker ID: 123
    Approve x5 of IDs 1-10 if you are on allowlist ABC,
}, {
    Tracker ID: 456,
    Approve x10 of IDs 1-10 if you are on allowlist CDE
}]
```

### Approval Trackers

Approval trackers are tallies that track how much of badges have been transferred and how many transfers take place. Each transfer increments **numTransfers** and each badge transferred increments the **amounts** in the interface below, if set and applicable.

```typescript
export interface ApprovalTrackerInfoBase<T extends NumberType> extends ApprovalTrackerIdDetails<T> {
  numTransfers: T;
  amounts: Balance<T>[];
}
```

The **trackerType** corresponds to what type of tracker it is.

If "overall", this is applicable to any transfer and will increment everytime the approval is used. This creates a single universal tally. **approvedAddress** will be empty.

If "to", "from", or "initiatedBy", we set **approvedAddress** to be the sender, recipient, or initiator of the transfer, respectively. Note since **approvedAddress** and **trackerType** are part of the approval tracker's ID, this creates unique individual tallies per address per tracker type.

Moving forward, let's say we are dealing with the collection approvals approving transfers from Bob for collection 1, so `1-collection- -uniqueID-?-?.` ?-? is trackerType-approvedAddress.

#### **Approval Amounts**

Approval amounts (**approvalAmounts**) allow you to specify the threshold amount that can be transferred for this approval. This is similar to other interfaces (such as approvals for ERC721), except we use an increment + threshold system as opposed to a decrement + greater than 0 system.

The amounts approved are limited to the **badgeIds** and **ownershipTimes** defined by the approval (see previous page). Also, note that the to addresses are bounded to the addresses in the **toList,** from addresses from the **fromList**, and initiated by addresses from the **initiatedByList**.

We define four levels (**trackerType** = "overall", "to", "from", "initiatedBy") that you can specify for approval amounts as seen below. You can define multiple if desired, and to be approved, the transfer must satisfy all.

* **Overall**: Overall will increment a universal, cumulative tally for all transfers that match this approval, regardless of who sends, receives, or initiates them.
* **Per To Address**: If you specify an approval amount per to address, we will create unique cumulative tallies for every unique "to" address.
* **Per From Address**: Creates unique cumulative tallies for every unique "from" address.
* **Per Initiated By Address**: Creates unique cumulative tallies for every unique "initiatedBy" address.
  * Ex: Each unique address can initiate a transfer for x1 of badge ID 1

If the amount set is nil value or "0", this means there is no limit (no amount restrictions).

```json
"collectionApprovals": [
    {
      "fromListId": "Bob",
      "toListId": "AllWithMint",
      "initiatedByListId": "AllWithMint",
      "transferTimes": [
        {
          "start": "1691931600000",
          "end": "1723554000000"
        }
      ],
      "ownershipTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ],
      "badgeIds": [
        {
          "start": "1",
          "end": "100"
        }
      ],
      "approvalId": "uniqueID",
      "amountTrackerId": "uniqueID",
      
      "approvalCriteria": {
        "approvalAmounts": {
           "overallApprovalAmount": "1000", 
           "perFromAddressApprovalAmount": "0", //no limit
           "perToAddressApprovalAmount": "0",
           "perInitiatedByAddressApprovalAmount": "10" //limit of x10
        },
        ...
      }
      ...
    }
  
```

So let's say we have the **approvalAmounts** defined above and Alice initiates a transfer of x10 from Bob. There are two separate trackers that get incremented here.

\#1) Tracker with the following ID `1-collection- -uniqueID-overall-` gets incremented to x10 out of 1000. Any subsequent transfers (say from Charlie) will also increment this overall universal tracker as well.

\#2) Tracker with ID `1-collection- -uniqueID-initiatedBy-alice`gets incremented to x10 out of 10 used. Alice has now fully used up her threshold for this tracker. This tracker is only incremented when Alice initiates the transfer. If Charlie initiates a transfer, his unique initiatedBy tracker will get incremented which is separate from Alice's.

Since there was an unlimited amount approved for the "to" and "from" trackers, we do not increment anything for those trackers (as-needed basis).

**Resets + ID Changes**

Let's say we update the **amountTrackerId** to "uniqueID2" from "uniqueID". This makes all tracker IDs different, and thus, all tallies will start from scratch.

`1-collection- -uniqueID-initiatedBy-alice` ->

`1-collection- -uniqueID2-initiatedBy-alice`

If in the future, you change back to "uniqueID", the starting point will be the previous tally. Using the examples above, x10/10 used for Alice's initiated by tracker.

### **Max Number of Transfers**

Similar to approval amounts, you can also specify the maximum number of transfers that can occur. These will be incremented by 1 each transfer.

<pre class="language-json"><code class="lang-json">"maxNumTransfers": {
<strong>    "overallMaxNumTransfers": "0",
</strong>    "perFromAddressMaxNumTransfers": "0",
    "perToAddressMaxNumTransfers": "0",
    "perInitiatedByAddressMaxNumTransfers": "1"
},
</code></pre>

So if we reuse the previous example where Alice initiates a transfer, the tracker `1-collection- -uniqueID-initiatedBy-alice`would increment to 1/1 transfers used. Alice can no longer initiate another transfer.

Again, nothing is incremented unnecessarily, so "overall", "to", and "from" trackers are not incremented in this case.

Note that sometimes, in [Predetermined Balances](approval-criteria.md#predetermined-balances), if you specify useOverallNumTransfers, usePerToAddressNumTransfers, usePerFromAddressNumTransfers, or usePerInitiatedByAddressNumTransfers, we do need the number of transfers for the corresponding tracker and do increment even if the corresponding maximum number of transfers for that level is not set / set to no limit.

### **Merkle Challenges**

```typescript
export interface MerkleChallenge<T extends NumberType> {
  root: string
  expectedProofLength: T;
  useCreatorAddressAsLeaf: boolean
  maxUsesPerLeaf: T
  uri: string
  customData: string
}
```

Merkle challenges allow you to define a SHA256 Merkle tree, and to be approved for each transfer, the initiator of the transfer must provide a valid Merkle path for the tree via **merkleProofs** in MsgTransferBadges.

For example, you can create a Merkle tree of claim codes. Then to be able to claim badges, each claimee must provide a valid Merkle path from the claim code to the **root**. You distribute the secret leaves in any method you prefer.

The **expectedProofLength** defines the expected length for the Merkle proofs to be provided. This is to avoid preimage and second preimage attacks. **All proofs must be of the correct length, which means you must design your trees accordingly.**

We also support a couple customization options.

First, you can set **useCreatorAddressAsLeaf**. If set, we will override the provided leaf of each Merkle proof with the msg.creator aka the Cosmos address of the initiator of the transaction. This can be used to implement allowlist trees. Note that the initiator must also be within the **initiatedByList** of the approval for it to make sense.

For allowlist trees (**useCreatorAddressAsLeaf** is true), **maxUsesPerLeaf** can be set to any number. "0" or null means unlimited uses. "1" means max one use per leaf and so on. When **useCreatorAddressAsLeaf** is false, this must be set to "1" to avoid replay attacks.For example, ensure that a code / proof can only be used once because once used once, the blockchain is public and anyone then knows the secret code.

We track this in a challenge tracker, similar to the approvals trackers explained above. We simply track if a leaf index (leftmost leaf = index 0, ...) has been used and only allow it to be used X many times, if constrained. Like approval trackers, this is increment only and non-deletable.

<pre class="language-json"><code class="lang-json"><strong>"merkleChallenge": {
</strong>   "root": "758691e922381c4327646a86e44dddf8a2e060f9f5559022638cc7fa94c55b77",
   "expectedProofLength": "1",
   "useCreatorAddressAsLeaf": true,
   "maxOneUsePerLeaf": true,
   "uri": "ipfs://Qmbbe75FaJyTHn7W5q8EaePEZ9M3J5Rj3KGNfApSfJtYyD",
   "customData": ""
}
</code></pre>

See Predetermined Balances below for reserving specific leaf indices for specific badges.

#### **Creating a Merkle Tree**

We provide the **treeOptions** field in the SDK to let you define your own build options for the tree (see [Compatibility](../bitbadges-api/designing-for-compatibility.md) with the BitBadges API / Indexer). You may experiment with this, but please test all Merkle paths and claims work as intended first. The only tested build options so far are what you see below with the fillDefaultHash.

```typescript
import { SHA256 } from 'crypto-js';
import MerkleTree from 'merkletreejs';

const codes = [...]
const hashedCodes = codes.map(x => SHA256(x).toString());
const treeOptions = { fillDefaultHash: '0000000000000000000000000000000000000000000000000000000000000000' }
const codesTree = new MerkleTree(hashedCodes, SHA256, treeOptions);
const codesRoot = codesTree.getRoot().toString('hex');
const expectedMerkleProofLength = codesTree.getLayerCount() - 1;
```

For allowlists, replace with this code.

```typescript
addresses.push(...toAddresses.map(x => convertToCosmosAddress(x)));

const addressesTree = new MerkleTree(addresses.map(x => SHA256(x)), SHA256, treeOptions)
const addressesRoot = addressesTree.getRoot().toString('hex');
```

A valid proof can then be created via where codeToSubmit is the code submitted by the user.

```typescript
const passwordCodeToSubmit = '....'
const leaf = isAllowlist ? SHA256(chain.cosmosAddress).toString() : SHA256(passwordCodeToSubmit).toString();
const proofObj = tree?.getProof(leaf, allowlistIndex !== undefined && allowlistIndex >= 0 ? allowlistIndex : undefined);
const isValidProof = proofObj && tree && proofObj.length === tree.getLayerCount() - 1;


const codeProof = {
  aunts: proofObj ? proofObj.map((proof) => {
    return {
      aunt: proof.data.toString('hex'),
      onRight: proof.position === 'right'
    }
  }) : [],
  leaf: isAllowlist ? '' : passwordCodeToSubmit,
}

const txCosmosMsg: MsgTransferBadges<bigint> = {
  creator: chain.cosmosAddress,
  collectionId: collectionId,
  transfers: [{
    ...
    merkleProofs: requiresProof ? [codeProof] : [],
    ...
  }],
};
```

### **Requires**

You also have the following options to further restrict who can transfer to who.

**requireToEqualsInitiatedBy, requireToDoesNotEqualsInitiatedBy**

**requireFromEqualsInitiatedBy, requireFromDoesNotEqualsInitiatedBy**

These are pretty self-explanatory. You can enforce that we additionally check if the to or from address equals or does not equal the initiator of the transfer. Note that this is bounded to the addresses in the respective lists for to, from, and initiatedBy.

### **Predetermined Balances**

Predetermined balances are a new way of having fine-grained control over the amounts that are approved with each transfer. In a tally-based system where you approve X amount to be transferred, you have no control over the combination of amounts that will add up to X. For example, if you approve x100, you can't control whether the transfers are \[x1, x1, x98] or \[x100] or another combination.

Predetermined balances let you explicitly define the amounts that must be transferred and the order of the transfers. For example, you can enforce x1 of badge ID 1 has to be transferred before x1 of badge ID 2, and so on.

**Pretty much, the transfer will fail if the balances are not EXACTLY as defined in the predetermined balances.**

```typescript
export interface PredeterminedBalances<T extends NumberType> {
  manualBalances: ManualBalances<T>[];
  incrementedBalances: IncrementedBalances<T>;
  orderCalculationMethod: PredeterminedOrderCalculationMethod;
}
```

**Precalculating Balances**

Predetermined balances can quickly change, even in between the time a transaction is broadcasted and confirmed. For example, other users' mints get processed, and thus, the badge IDs one should receive changes. This creates a problem because you can't manually specify balances because that results in race conditions and failed transfers / claims.

To combat this, when initiating a transfer, we allow you to specify **precalculateBalancesFromApproval** (in MsgTransferBadges). Here, you define which **approvalId** you want to precalculate from, and at execution time, we calculate what the successful predetermined balances are and override the requested balances to transfer with them. Note this is the unique **approvalId** of the approval, not the tracker ID.

<pre class="language-typescript"><code class="lang-typescript"><strong>precalculateBalancesFromApproval: {
</strong>    approvalId: string;
    approvalLevel: string; //"collection" | "incoming" | "outgoing"
    approverAddress: string; //"" if collection-level
}
</code></pre>

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
* **Incremented Balances:** Define starting balances and then define how much to increment the IDs and times by after each transfer. This is how to implement the above example: you can enforce x1 of badge ID 1 has to be transferred before x1 of badge ID 2, and so on. This is typically used for minting badges.
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

The order is calculated by a specified order calculation method.

For manual balances, we want to determine which element index of the array is transferred (e.g. order number = 0 means the balances of manualBalances\[0] will be transferred). For incremented balances, this corresponds to how many times we should increment (e.g. order number = 5 means apply the increments to the starting balances five times).

There are five calculation methods to determine the order method. We either use a running tally of the number of transfers to calculate the order number (no previous transfers = order number 0, one previous = order number 1, and so on). This can be done on an overall or per to/from/initiatedBy address basis and is incremented using an approval tracker as explained in [Max Number of Transfers](approval-criteria.md#max-number-of-transfers). Note this is the same tracker. So even if you have unlimited number of transfers defined in **maxNumTransfers,** this will increment it.

We also support using the leaf index for the defined Merkle challenge proof (see [Merkle Challenges](approval-criteria.md#merkle-challenges)) to calculate the order number (e.g. leftmost leaf will correspond to order number 0, next leaf will be order number 1, and so on). The leftmost leaf means the leftmost leaf of the **expectedProofLength** layer.

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

**Overlap / Out of Bounds**

Sometimes, the order number may correspond to **badgeIds** and **ownershipTimes** that are out of bounds of the approval, with either no overlap or some overlap.

If it is completely out of bounds (e.g. order number = 101 but approved badgeIds 1-100 with increments of 1), this is practically ignored. This is because if you try and transfer badge ID 101, it will never match to the current approval.

In rare cases, there may be overlap (some in bounds and some out of bounds). The overall transfer balances still must be exactly as defined (in bounds + out of bounds); however, we only approve the in bounds ones for the current approval. The out of bounds ones must be approved by a separate approval.

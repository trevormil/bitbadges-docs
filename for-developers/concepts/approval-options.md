# ✅ Approval Options

In the last page, we mainly talked about how we match to an approved transfer. Here, we will talk about the **approvalDetails.** These are the options or restrictions you can set which decide whether a transfer is approved or not (e.g. how much can be transferred? how many times? etc). To be approved, it must satisfy all the options / restrictions set.

```typescript
export interface ApprovalDetails<T extends NumberType> {
  approvalId: string;
  uri: string;
  customData: string;

  mustOwnBadges: MustOwnBadges<T>[];
  merkleChallenges: MerkleChallenge<T>[];
  predeterminedBalances: PredeterminedBalances<T>;
  approvalAmounts: ApprovalAmounts<T>;
  maxNumTransfers: MaxNumTransfers<T>;

  requireToEqualsInitiatedBy: boolean;
  requireFromEqualsInitiatedBy: boolean;
  requireToDoesNotEqualInitiatedBy: boolean;
  requireFromDoesNotEqualInitiatedBy: boolean;

  overridesFromApprovedOutgoingTransfers: boolean;
  overridesToApprovedIncomingTransfers: boolean;
}
```



**Approval ID**

Approval IDs are simply a string identifier for the approval and must be unique per approved transfers timeline per address (collection-wide approved transfers are also considered a unique approved transfers timeline). The approval IDs are used by the blockchain to fetch / store / tally the number of transfers and amounts transferred for the approval (if applicable).

So, if a user's incoming approved transfers uses an approval ID that is also in their outgoing approved transfers, this is fine because they are on different timelines ("collection", "incoming", "outgoing"). These IDs will have no overlap. But, a user's incoming approvals cannot specify two with the same approval ID.&#x20;

**Approval Amounts**

Approval amounts (**approvalAmounts**) specifies the threshold amount that can be transferred for this approval. This is similar to other interfaces (such as approvals for ERC721).&#x20;

Whenever a transfer matches an approval which specifies a threshold approval amount, we increment a running tally of the cumulative amounts transferred and assert it does not exceed the specified threshold. This is implemented slightly different than other token interfaces, which often decrement the threshold directly.

We define four levels that you can specify for approval amounts as seen below. You can define multiple if desired. To be approved, the transfer must satisfy all that are specified. If nil or zero, we assume that there are no restrictions and will be approved for that level.

* **Overall**: Overall will increment the cumulative tally for all transfers that match this approval, regardless of who sends, receives, or initiates them.
* **Per To Address**: If you specify an approval amount per to address, we will create unique cumulative tallies for every unique "to" address.&#x20;
  * Ex: Each unique address can only claim x1 of badge ID 1
* **Per From Address**: Creates unique cumulative tallies for every unique "from" address.&#x20;
* **Per Initiated By Address**: Creates unique cumulative tallies for every unique "initiatedBy" address.&#x20;

Note that each level will also create a unique tally. A "per to" addresses tally for Bob will be a different tally than the "per from" addresses tally for Bob. The "per to" one will only increment when Bob is the "to" address, and the "per from" will only increment when Bob is the "from" address.

Tallies are also unique to each approval ID. So, an "overall" tally for approval ID 123 will be different than the overall tally for approval ID ABC.&#x20;

Each tally is increment-only and non-deletable. If you would like to reset the state of the tally, a new unused approval ID can be specified which will create brand new tallies.&#x20;

IMPORTANT: note that we only increment tallies if a threshold amount is currently defined. For example, if no "per to" address threshold is defined, we will not increment / create a tally. If you later set a "per to" address threshold, it is not retroactive and will start the tally from zero.

**Max Number of Transfers**

Similar to approval amounts, you can also specify the maximum number of transfers that can occur. This similarly creates an incrementing tally on four levels (overall, per-to address, per-from address, and per-initiatedBy address). Each transfer increments the tally by one, and we assert that we do not exceed the maximum number of transfers.

Storage is the same as approval amounts (e.g. increment only, only increments if specified, non-deletable, unique per timeline per approval ID, etc). See the section above.

The only difference is that we also may need the number of transfers to calculate the predetermined balances (see next section). If we do need this, we increment the number of transfers (even if the corresponding maximum number of transfers for that level is not set / set to no limit).

**Predetermined Balances**

Predetermined balances are a new way of having fine-grained control over the amounts that are approved with each transfer.&#x20;

In a tally-based system where you approve X amount to be transferred, you have no control over the combination of amounts that will add up to X. For example, if you approve x100, you can't control whether the transfers are \[x1, x1, x98] vs \[x100] or another combination.

Predetermined balances let you explicitly define the amounts that must be transferred and the order of the transfers. For example, you can enforce x1 of badge ID 1 has to be transferred before x1 of badge ID 2, and so on.

There are two ways to define the balances. Either one or the other method must be used but not together.

* **Manual Balances:** Simply define an array of balances manually.&#x20;
* **Incremented Balances:** Define starting balances and then define how much to increment the IDs and times by after each transfer. This is how to implement the above example: you can enforce x1 of badge ID 1 has to be transferred before x1 of badge ID 2, and so on.

The order is calculated by the order calculation method. For manual balances, this corresponds to the element index of the array (e.g. order number = 0 means first element in array). For incremented balances, this corresponds to how many times we should increment (e.g. order number = 5 means apply the increments to the starting balances five times).

There are five calculation methods. We either use a running tally of the number of transfers to calculate the order number (no previous transfers = order number 0, one previous = order number 1, and so on). This can be done on an overall or per to/from/initiatedBy address basis, similar to the approval amounts and maximum number of transfers.&#x20;

We also support using the leaf index for a Merkle proof to calcualte the order number (e.g. leftmost leaf will correspond to order number 0, next leaf will be order number 1, and so on). The leftmost leaf means the leftmost leaf of the expectedProofLength layer, and we use the challenge which has useLeafIndexForTransferOrder set to true (see Merkle Challenges below). Exactly one challenge must have useLeafIndexForTransferOrder set to true if using this option.

This can be used to reserve specific badges for specific users / claim codes.&#x20;

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

**Must Own Badges**

Must own badges are another unique feature that is very powerful. This allows you to specify certain badges and amounts of each badge that must be owned at custom times in order to be approved. &#x20;

For example, you may implement a badge collection where only holders of a verified badge are approved to send and receive badges.

Note that collections with "Off-Chain" balances are not supported for this feature. "Inherited" balances are not currently supported, but this is coming soon.

**Merkle Challenges**

Merkle challenges allow you to define a SHA256 Merkle tree, and to be approved for each transfer, the initiator of the transfer must provide a valid Merkle path for the tree. For example, you can create a Merkle tree of claim codes. Then to be able to claim badges, each claimee must provide a valid Merkle path from the claim code to the **root**.

The **expectedProofLength** defines the expected length for the Merkle proofs to be provided. This is to avoid preimage and second preimage attacks. All proofs must be of the correct length, which means you must design your trees accordingly.

We also support a couple options. First, you can set **useCreatorAddressAsLeaf**. If set, we will override the provided leaf of each Merkle proof with the msg.creator or initiator of the transaction. This can be used to implement whitelist trees. Note that the initiator must also be within the initiatedByMapping of the approval for it to make sense.

The **maxOneUsePerLeaf** is important to be set if you want to prevent against replay attacks. For example, ensure that a code / proof can only be used once.

Similar to approval IDs, the challenge IDs are unique per approved transfers timeline. If **maxOneUsePerLeaf** is specified, we create a store identified by the challenge ID which tracks which leaf has been used or not. This is increment-only and non-deletable. To reset the state, you must provide a new unused challenge ID. Challenge IDs can be reused across the same timeline (in different approvals). This means that we use the same store (which leaf has been used) for all the corresponding approvals, but this is not recommended because it can cause confusion.&#x20;

```typescript
export interface MerkleChallenge<T extends NumberType> {
  root: string
  expectedProofLength: T;
  useCreatorAddressAsLeaf: boolean
  maxOneUsePerLeaf: boolean
  useLeafIndexForTransferOrder: boolean
  challengeId: string
  uri: string
  customData: string
}
```

**Overrides**

As mentioned, the collection-wide approvals can override the user-level approvals. This is done via the **overridesFromApprovedOutgoingTransfers** or **overridesToApprovedIncomingTransfers**.&#x20;

If this respective approval is matched to with an override flag set to true, we will not check the user approvals for the matched criteria. If override flag is not set, we will have to check that it is also approved on the user level.

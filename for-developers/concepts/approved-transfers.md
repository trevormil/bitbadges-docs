# 🤝 Approved Transfers

First, read [Transferability ](../../overview/how-it-works/transferability.md)for an overview of approved transfers. It is also important you understand how our [First-Match Only](first-match-only.md) design pattern works.

Note: The [Approved Transfers](approved-transfers.md) and [Permissions ](../../overview/how-it-works/permissions.md)are the most powerful features of the interface, but they can also be the most confusing.&#x20;

For further examples, please reference the [Learn the Interface](../learn-the-interface/) section.

**Approved Transfers**

When referring to approved transfers, there are three levels of approved transfers: collection, incoming, and outgoing as previously explained. All the interfaces for these three are the exact same minus the following functionality:

* The "to" fields are removed for incoming approvals and "from" fields are removed for outgoing approvals because they will automatically be populated with the respective user's address
* The collection level has an option to override the user-level approvals but not vice-versa.

**Genesis Values**

For the incoming and outgoing approvals, the collection can optionally specify default values for the incoming / outgoing approvals via **defaultApprovedIncomingTransfersTimeline** and **defaultApprovedOutgoingTransfersTimeline**. This default is used when first initiating a balance for the first time for an account (see [Default User Approvals](../learn-the-interface/default-user-approvals.md)).

**Default Approvals**

For ease of use, we will always approve outgoing transfers where from equals initiatedBy (i.e. initiated by the user) and approve incoming transfers where to equals initiatedBy, unless explicitly disallowed in the approvals.

**Approved vs Unapproved**

For all individual levels, we assume that the transfer is UNAPPROVED unless explicitly approved in the array (according to the above defaults and manipulations). Meaning, if there is no match, it is UNAPPROVED by default.

For a transfer to be approved overall, it has to satisfy the collection-level approvals, and if not overriden by the collection-level, the user incoming / outgoing also have to be satisfied.

**Escrows**

Note that the approved transfers are just approvals and may not line up with the underlying balances. It is the approver's responsibility to make sure that enough balances are owned to not mess up the approval (including accounting for any potential revokes or freezing). This includes the manager ensuring adequate balances for all transfers from the "Mint" address.

For example, Alice can approve Bob to transfer x100 of badge IDs 1 - 100, but if Alice doesn't have adequate balances, Bob cannot complete the transfer. Or if Alice did have enough but some are revoked by the manager, she may not have enough at transfer time.

**Representing Transfers**

To represent specific transfers in the approvals interface, we use six fields for representing each transfer (toMapping, fromMapping, initiatedByMapping, transferTimes, badgeIds, ownedTimes).

* toMapping, fromMapping, initiatedByMapping: These are all [AddressMappings](address-mappings.md) specifying which addresses can transfer to which addresses, and the transfer can be initiatedBy which addresses.
* transferTimes: When does the transfer takes place? A [UintRange](uint-ranges.md)\[] of times (UNIX milliseconds).
* badgeIds: What badge IDs are being transferred? A [UintRange](uint-ranges.md)\[] of badge IDs.
* ownedTimes: What ownership times for the badges are being transferred?

Even though we use range logic (AddressMappings and UintRanges) for representing approved transfers as shown above, in reality, it can be thought of as broken down into the necessary amount of (to, from, initiatedBy, transferTime, badgeId, ownedTime) tuples of **singular** values.&#x20;

For example, the tuple with range logic (bob, alice, bob, 10, <mark style="color:blue;">\[1-2], \[10-11]</mark>) can be broken down into smaller tuples of (bob, alice, bob, 10, <mark style="color:blue;">1, 10</mark>), (bob, alice, bob, 10, <mark style="color:blue;">2, 10</mark>), (bob, alice, bob, 10, <mark style="color:blue;">1, 11</mark>), and (bob, alice, bob, 10, <mark style="color:blue;">2, 11</mark>).&#x20;

The same can logic can be applied for breaking down address mappings into single addresses.

**Allowed Combinations**

All approved transfers have an **allowedCombinations** field as seen in the interface below. This field lets you manipulate (invert, override with all, or override with none) the specified default values (i.e. the toMapping, fromMapping, initiatedByMapping, transferTimes, badgeIds, ownedTimes) to create different combinations which can be either set to approved (**isAllowed** = true) or unapproved (**isAllowed** = false). It is mainly used for shorthand and avoiding repeating values.

For example, this allows you to potentially approve all transfers with Badge IDs 1-10 but disapprove all transfers with the inverse (10 - Max ID).&#x20;

Note that even if there are no manipulations of the default values, you still need to have at least one combination which sets **isAllowed** to be true or false.&#x20;

```typescript
export interface CollectionApprovedTransfer<T extends NumberType> {
  toMappingId: string;
  fromMappingId: string;
  initiatedByMappingId: string;
  transferTimes: UintRange<T>[];
  badgeIds: UintRange<T>[];
  ownedTimes: UintRange<T>[];
  allowedCombinations: IsCollectionTransferAllowed[];
  approvalDetails: ApprovalDetails<T>[];
}

export interface ValueOptions {
  invertDefault: boolean;
  allValues: boolean;
  noValues: boolean;
}

export interface IsCollectionTransferAllowed {
  toMappingOptions: ValueOptions;
  fromMappingOptions: ValueOptions;
  initiatedByMappingOptions: ValueOptions;
  transferTimesOptions: ValueOptions;
  badgeIdsOptions: ValueOptions;
  ownedTimesOptions: ValueOptions;
  isAllowed: boolean;
}
```

**Approval Details**

The **approvalDetails** can be thought of as the actual restrictions for the approval. How much can be approved? how many transfers? Etc.

We have dedicated a page to just explaining the [approval details here](approval-options.md).&#x20;

For the rest of this page, you can simply think of it as the challenges or restrictions that need to be obeyed to be approved (in addition to having isAllowed = true).

**Matching Transfers to Approvals**

Approved transfers are stored in a linear array with a [First-Match Only](first-match-only.md) policy. The match algorithm is as follows:

1. Expand the approved transfers array with the manipulations specified in **allowedCombinations,** maintaining order. This should leave us with a list of transfer tuples using range logic (toMapping, fromMapping, initiatedByMapping, transferTimes, badgeIds, ownedTimes) -> (isAllowed, approvalDetails).
2. Expand all approved transfer tuples to their singular tuple values, as explained in Representing Transfers above. We should now have a list of singular transfer tuples -> (isAllowed, approvalDetails).
3. Expand the current transfer tuple that is to be checked to its singular tuple value. This leaves us with a list of transfer tuples to check.
4. Find the corresponding tuples in the expanded list from step 2 for all the tuples of the transfer to check from step 3. This is done using a first-match only policy (i.e. we do a linear scan of the array and only take the first matches). It is considered a match if ALL criteria are the same (i.e. the transfer tuple of singular values is equal).
5. Check if approved using the respective isAllowed and details.

Ex: Let's say we are checking the transfer (bob, alice, bob, 10, <mark style="color:blue;">1-2, 10-100</mark>) with the approved transfers \[(bob, alice, bob, 10, <mark style="color:blue;">\[1-2],</mark> <mark style="color:red;">\[1000-2000]</mark>) -> (true, approvalDetails), (bob, alice, bob, 10, <mark style="color:blue;">\[1-2], \[10-50]</mark>) -> (true, approvalDetails), (bob, alice, bob, 10, <mark style="color:blue;">\[1-2], \[51-100]</mark>) -> (false, approvalDetails)].

We would be approved for (bob, alice, bob, 10, <mark style="color:blue;">\[1-2], \[10-50]</mark>), assuming it obeys the approvalDetails.

However, we would not be approved for (bob, alice, bob, 10, <mark style="color:blue;">\[1-2], \[51-100]</mark>).

The transfer never matches with the (bob, alice, bob, 10, <mark style="color:blue;">\[1-2],</mark> <mark style="color:red;">\[1000-2000]</mark>) approval because the ownedTimes never overlap.

**First-Match Only Example**

By first match only, we mean that if we have ("All", "All", "All", 1-100, <mark style="color:blue;">1-10</mark>, 1-10) -> approval1 and ("All", "All", "All", 1-100, <mark style="color:blue;">1-5</mark>, 1-10) -> approval2, approval2 will never be used because we there would be duplicate tuples ("All", "All", "All", 1-100, <mark style="color:blue;">1-5</mark>, 1-10) when broken down, and we would only use the first linear match which is approval1.

Also note that to match, ALL criteria must match. So, ("All", "All", "All", 1-100, <mark style="color:blue;">1-10</mark>, 1-10) and ("All", "All", "All", 1-100, <mark style="color:blue;">11-20</mark>, 1-10) would not match at all.&#x20;

**Difference From UpdateApprovedTransferPermission**

While this may seem similar to the UpdateApprovedTransferPermission, the permission corresponds to the updatability of the approved transfers.

The approved transfers themselves correspond to if a transfer is currently approved or not.

**Examples**

See [tutorials ](../learn-the-interface/)for further examples.

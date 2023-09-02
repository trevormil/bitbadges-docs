# 🤝 Approved Transfers





First, read [Transferability ](../../overview/concepts/transferability.md)for an overview of approved transfers. It is also important you understand how our [First-Match Only](first-match-only.md) design pattern works.

Note: The [Approved Transfers](approved-transfers.md) and [Permissions ](../../overview/concepts/manager.md)are the most powerful features of the interface, but they can also be the most confusing.&#x20;

For further examples, please reference the [Learn the Interface](../learn-the-interface/) section.

## Understanding Approved Transfers and First-Match Only Design

To comprehend the intricacies of the interface's approved transfers and the underlying first-match only design, it's vital to explore the following concepts and mechanisms.

### Approved Transfers Overview

#### Approval Levels

Approved transfers encompass three hierarchical levels: collection, incoming, and outgoing, as previously elaborated. These levels facilitate controlled and secure transfers. The interfaces for these three levels share common elements, with slight variations in functionality:

* Incoming approvals exclude the "to" fields as they are automatically populated with the recipient's address.
* Outgoing approvals omit the "from" fields, as they are automatically filled with the sender's address.
* The collection level holds the capacity to override user-level approvals, but not vice versa.

#### Genesis Values and Default Approvals

For incoming and outgoing approvals, the collection can define default values via `defaultApprovedIncomingTransfersTimeline` and `defaultApprovedOutgoingTransfersTimeline`. These defaults are applied when initializing a balance for the first time in an account  (see [Default User Approvals](../learn-the-interface/default-user-approvals.md)).

Also, approvals are automatically set to be approved for outgoing transfers where the sender is the same as the initiator. Similarly, incoming transfers where the recipient matches the initiator's address are approved by default, unless explicitly disallowed in the approvals.

#### Matching and Escrows

When it comes to approved transfers, it's important to note that they are essentially approvals and may not necessarily correspond directly to underlying balances. Approvers must ensure that sufficient balances are available to uphold the approvals' integrity, accounting for potential revokes or freezes. This responsibility extends to the collection-level transferability ensuring adequate balances for transfers from the "Mint" address.

**Approved vs Unapproved**

For all individual levels, we assume that the transfer is UNAPPROVED unless explicitly approved in the array (according to the above defaults and manipulations). Meaning, if there is no match, it is UNAPPROVED by default.

For a transfer to be approved overall, it has to satisfy the collection-level approvals, and if not overriden by the collection-level, the user incoming / outgoing also have to be satisfied.

### First-Match Only Design

The first-match only design pattern serves as a cornerstone in the interface's structure. It prioritizes the initial match in the presence of duplicates, enhancing efficiency and minimizing redundancy.

#### Representation and Expansion

To represent transfers, six fields are used: `toMapping`, `fromMapping`, `initiatedByMapping`, `transferTimes`, `badgeIds`, and `ownershipTimes`. These fields collectively define the transfer's attributes, such as the addresses involved, timing, and badge details. This representation leverages range logic, breaking down into individual tuples for enhanced comprehension.

* toMapping, fromMapping, initiatedByMapping: These are all [AddressMappings](address-mappings-lists.md) specifying which addresses can transfer to which addresses, and the transfer can be initiatedBy which addresses.
* transferTimes: When does the transfer takes place? A [UintRange](uint-ranges.md)\[] of times (UNIX milliseconds).
* badgeIds: What badge IDs are being transferred? A [UintRange](uint-ranges.md)\[] of badge IDs.
* ownershipTimes: What ownership times for the badges are being transferred?

#### Allowed Combinations

All approved transfers incorporate an `allowedCombinations` field. This field allows manipulations of default values, enabling actions like inversion, overriding with all values, or overriding with none. This manipulation offers shorthand solutions and avoids repetition.&#x20;

Note that even if you do not use any of the manipulation options, an empty combination must still be defined. In other words, even if there are no manipulations of the default values, you still need to have at least one combination defined. An empty array is not allowed.

```
[
    {}
]
```

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

#### Approval Details

The `approvalDetails` section corresponds to additional restrictions or challenges necessary for approval. It defines aspects like the quantity approved, maximum transfers, and more.

Note that this field is not considered in the first-match mechanism. We only match based on the six fields previously mentioned.

We have dedicated a page to just explaining the [approval details here](approval-options.md).&#x20;

For the rest of this page, you can simply think of it as the challenges or restrictions that need to be obeyed to be approved (in addition to having isAllowed = true).

#### Matching Transfers to Approvals

The process of matching transfers to approvals involves several steps:

1. Expand the approved transfers array using the `allowedCombinations` manipulations while maintaining order.&#x20;
2. Expand all approved transfer tuples to singular tuple values.
3. Expand the current transfer tuple to be checked.
4. Find corresponding tuples in the expanded list for all transfer tuples to be checked using our [First-Match policy](first-match-only.md).
5. Check the entire transfer for approval using `isAllowed` and `approvalDetails`.

### Illustrative Example

Let's consider a scenario:

* Transfer: `(bob, alice, bob, 10, 1-2, 10-100)`
* Approved Transfers:
  * `(bob, alice, bob, 10, [1-2], [1000-2000]) -> (true, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [10-50]) -> (true, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [51-100]) -> (false, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [10-100]) -> (true, approvalDetails)`
    * This last tuple is practically ignored because of the first-match policy.

In this case, the transfer would be approved for `(bob, alice, bob, 10, [1-2], [10-50])`, provided it adheres to `approvalDetails`. However, it wouldn't be approved for `(bob, alice, bob, 10, [1-2], [51-100])` due to incompatible owned times.

See [tutorials ](../learn-the-interface/)for further examples.

### **Difference From UpdateApprovedTransferPermission**

While this may seem similar to the UpdateApprovedTransferPermission, the permission corresponds to the **updatability** of the approved transfers.

The approved transfers themselves correspond to if a transfer is currently approved or not.

### Conclusion

Navigating the realm of approved transfers and the first-match only design demands a comprehensive understanding of these intricate mechanics. To grasp the concepts further, additional examples and resources can be referenced in the "Learn the Interface" section.

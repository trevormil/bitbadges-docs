# 🤝 Approvals

First, read [Transferability ](../../overview/concepts/transferability.md)for an overview of approved transfers.

Note: The [Approved Transfers](approvals.md) and [Permissions ](../../overview/concepts/manager.md)are the most powerful features of the interface, but they can also be the most confusing. Please ask for help if needed. For further examples, please reference the [Learn the Interface](../learn-the-interface/) section.

### Approved Transfers Overview

#### Approval Levels

Approved transfers encompass three hierarchical levels: collection, incoming, and outgoing, as previously elaborated. The interfaces for these three levels share common elements, with slight variations in functionality:

* Incoming approvals exclude the "to" fields as they are automatically populated with the recipient's address.
* Outgoing approvals omit the "from" fields, as they are automatically filled with the sender's address.
* The collection level holds the capacity to override user-level (incoming / outgoing) approvals, but not vice versa.

For a transfer to be approved overall, it has to satisfy the collection-level approvals, and if not overriden by the collection-level, the user incoming / outgoing also have to be satisfied.

#### Genesis Values and Default Approvals

For incoming and outgoing approvals, the collection can define default values via `defaultApprovedIncomingTransfers` and `defaultApprovedOutgoingTransfers`. These defaults are applied when initializing a balance for the first time in an account  (see [Default User Approvals](../learn-the-interface/default-user-approvals.md)).

Also, we automatically add an unlimited approval (no amount restrictions) in the a) user's outgoing approvals where the sender is the same as the initiator and also in the b) user's incoming approvals where the recipient is the same as the initiator.

#### Matching and Escrows

When it comes to approved transfers, it's important to note that they are just approvals and may not necessarily correspond directly to underlying balances. Approvers must ensure that sufficient balances are available to uphold the approvals' integrity, accounting for potential revokes or freezes. This responsibility extends to the collection-level transferability. such as ensuring adequate balances for transfers from the "Mint" address.

On a similar note, if approvals become no longer valid (such as approving a badge but it was revoked via a different approval), the former approval doesn't automatically get cancelled.

#### Approval Details

The `approvalDetails` section corresponds to additional restrictions or challenges necessary to be satisfied for approval. It defines aspects like the quantity approved, maximum transfers, and more.

We have dedicated a page to just explaining the [approval details here](approval-options.md).&#x20;

For the rest of this page, you can simply think of it as the challenges or restrictions that need to be obeyed to be approved (in addition to having isApproved = true).

















#### Representation and Expansion

To represent transfers, six main fields are used: `toMapping`, `fromMapping`, `initiatedByMapping`, `transferTimes`, `badgeIds`, and `ownershipTimes`. These fields collectively define the transfer's attributes, such as the addresses involved, timing, and badge details. This representation leverages range logic, breaking down into individual tuples for enhanced comprehension.

* toMapping, fromMapping, initiatedByMapping: These are all [AddressMappings](address-mappings-lists.md) specifying which addresses can transfer to which addresses initiatedBy which addresses.
* transferTimes: When does the transfer takes place? A [UintRange](uint-ranges.md)\[] of times (UNIX milliseconds).
* badgeIds: What badge IDs are being transferred? A [UintRange](uint-ranges.md)\[] of badge IDs.
* ownershipTimes: What ownership times for the badges are being transferred?



For example, we might have the following:

* `(Mint, alice, bob, [1-10], [1-2], [1000-2000]) -> APPROVED`
* `(AllWithoutMint, alice, All [1-10], [1-2], [1-1000]) -> DISAPPROVED`



At execution time, even though the interface uses range logic, we break everything down into single value tuples, such as (bob, alice, bob, 1, 1, 1000) and check each singular value tuple separately. See example further down below.

#### Shorthand Combinations

All approved transfers incorporate an `allowedCombinations` field. This field allows manipulations of default values, enabling actions like inversion, overriding with all values, or overriding with none. This manipulation offers shorthand solutions and avoids repetition.&#x20;

Note that even if you do not use any of the manipulation options, an empty combination must still be defined. In other words, even if there are no manipulations of the default values, you still need to have at least one combination defined. An empty array is not allowed.

<pre><code><strong>[
</strong>    {}
]
</code></pre>

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
  isApproved: boolean;
}
```













**Approved vs Unapproved**

Approvals are simply a set of criteria, so it is entirely possible the same transfer can map to multiple approvals (or disapprovals).

We handle approvals **per level** in the following ways:

1. If the transfer matches to ANY disapproval, it is considered disallowed, regardless if it matches with any other approval(s).
2. If the transfer is unhandled (doesn't match to any approval or disapproval), it is DISAPPROVED by default.
3. If the transfer matches to multiple approvals, we take first-match (linear scan) by default. However, we allow the user to specify **prioritizedApprovals** and **onlyCheckPrioritizedApprovals** when transferring (see MsgTransferBadges), so they can only use their desired approvals.&#x20;

We recommend designing approvals in a way where all are mutually exclusive, meaning no transfer can map to multiple. This improves the simplicity and readability of your collection.

#### Matching Transfers to Approvals

The process of matching transfers to approvals involves several steps:

1. Expand the approved transfers array using the `allowedCombinations` manipulations while maintaining order.&#x20;
2. Expand all approval tuples to singular tuple values.
3. Expand the current transfer tuple to singular tuple values.
4. Find all matches.&#x20;
   1. If anything is disapproved or unhandled on any level, the overall transfer is disapproved.
   2. Match everything one to one (first match by default and check **prioritizedApprovals** and **onlyCheckPrioritizedApprovals**).
5. Check the `approvalDetails` for each one-to-one match and ensure it satisfies all criteria.

See example below.

### Example

Let's consider a scenario:

* Transfer: `(bob, alice, bob, 10, 1-2, 10-100)`
* Approved Transfers:
  * `(bob, alice, bob, 10, [1-2], [1000-2000]) -> (true, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [10-50]) -> (true, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [51-100]) -> (false, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [10-100]) -> (true, approvalDetails)`
    * This last tuple is practically ignored because of the first-match policy.

In this case, using first matches, the transfer would be approved for `(bob, alice, bob, 10, [1-2], [10-50])`, provided it adheres to `approvalDetails`. However, it wouldn't be approved for `(bob, alice, bob, 10, [1-2], [51-100])` due to incompatible owned times. Thus, overall, the transfer would be DISAPPROVED.

See [tutorials ](../learn-the-interface/)for further examples.

### **Difference From UpdateApprovedTransferPermission**

While this may seem similar to the UpdateApprovedTransferPermission, the permission corresponds to the **updatability** of the approved transfers.

The approved transfers themselves correspond to if a transfer is currently approved or not.

### Conclusion

To grasp the concepts further, additional examples and resources can be referenced in the "Learn the Interface" section.

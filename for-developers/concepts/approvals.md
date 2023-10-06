# 🤝 Approvals

First, read [Transferability ](../../overview/concepts/transferability.md)for an overview of approved transfers.

Note: The [Approved Transfers](approvals.md) and [Permissions ](../../overview/concepts/manager.md)are the most powerful features of the interface, but they can also be the most confusing. Please ask for help if needed. For further examples, please reference the [Learn the Interface](../learn-the-interface/) section.

## Approved Transfers Overview

### Approval Levels

Approved transfers encompass three hierarchical levels: collection, incoming, and outgoing, as previously elaborated. The interfaces for these three levels share common elements, with slight variations in functionality:

* Incoming approvals exclude the "to" fields as they are automatically populated with the recipient's address.
* Outgoing approvals omit the "from" fields, as they are automatically filled with the sender's address.
* The collection level holds the capacity to override user-level (incoming / outgoing) approvals, but not vice versa.

For a transfer to be approved overall, it has to satisfy the collection-level approvals, and if not overriden by the collection-level, the user incoming / outgoing also have to be satisfied.

### Genesis Values and Default Approvals

For incoming and outgoing approvals, the collection can define default values via `defaultIncomingApprovals` and `defaultOutgoingApprovals`. These defaults are applied when initializing a balance for the first time in an account  (see [Default User Approvals](../learn-the-interface/default-user-approvals.md)).

Also, we automatically add an unlimited approval (no amount restrictions) in the a) user's outgoing approvals where the sender is the same as the initiator and also in the b) user's incoming approvals where the recipient is the same as the initiator.

### Matching and Escrows

When it comes to approved transfers, it's important to note that they are just approvals and may not necessarily correspond directly to underlying balances. Approvers must ensure that sufficient balances are available to uphold the approvals' integrity, accounting for potential revokes or freezes. This responsibility extends to the collection-level transferability. such as ensuring adequate balances for transfers from the "Mint" address.

On a similar note, if approvals become no longer valid (such as approving a badge but it was revoked via a different approval), the former approval doesn't automatically get cancelled.

### Representation

```typescript
export interface CollectionApproval<T extends NumberType> {
  toMappingId: string;
  fromMappingId: string;
  initiatedByMappingId: string;
  transferTimes: UintRange<T>[];
  badgeIds: UintRange<T>[];
  ownershipTimes: UintRange<T>[];
  approvalId: string;
  approvalTrackerId: string;
  challengeTrackerId: string;

  uri?: string;
  customData?: string;
  toMappingOptions?: ValueOptions;
  fromMappingOptions?: ValueOptions;
  initiatedByMappingOptions?: ValueOptions;
  transferTimesOptions?: ValueOptions;
  badgeIdsOptions?: ValueOptions;
  ownershipTimesOptions?: ValueOptions;
  approvalTrackerIdOptions?: ValueOptions;
  challengeTrackerIdOptions?: ValueOptions;

  isApproved: boolean;
  approvalCriteria?: ApprovalCriteria<T>;
}
```

#### Approvals vs Disapprovals

We allow you to create approvals and disapprovals based on whether **isApproved** = true or false. If you are creating a disapproval (**isApproved** = false), the approval fields like **approvalCriteria**, etc will be ignored.

**Approved vs Unapproved**

Approvals are simply a set of criteria, so it is entirely possible the same transfer can map to multiple approvals (or disapprovals).

We handle approvals **per level** in the following ways:

1. If the transfer matches to ANY disapproval, it is considered disallowed, regardless if it matches with any other approval(s).
2. If the transfer is unhandled (doesn't match to any approval or disapproval), it is DISAPPROVED by default.
3. If the transfer matches to multiple approvals, we take first-match (linear scan) by default. However, we allow the user to specify **prioritizedApprovals** and **onlyCheckPrioritizedApprovals** when transferring (see MsgTransferBadges), so they can only use up their desired approvals.&#x20;

We recommend designing approvals in a way where all are mutually exclusive, meaning no transfer can map to multiple. This improves the simplicity and readability of your collection.

#### Approval ID

All approvals where **isApproved** = true must have a unique **approvalId** for identification per level. The **approvalTrackerId** and **challengeTrackerId** are different, and we will explain those on the following page along with **approvalCriteria**.

**Metadata**

We provide an optional **uri** and **customData** to allow you to add a link to something about your approval. We do not use it by default but it is provided if needed.

**Main Fields**

To represent transfers, six main fields are used: `toMapping`, `fromMapping`, `initiatedByMapping`, `transferTimes`, `badgeIds`, and `ownershipTimes`. These fields collectively define the transfer details, such as the addresses involved, timing, and badge details. This representation leverages range logic, breaking down into individual tuples for enhanced comprehension.

* **toMapping, fromMapping, initiatedByMapping**: These are all [AddressMappings](address-mappings-lists.md) specifying which addresses can transfer to which addresses and the transaction can be initiated by which addresses. If we use **toMappingId, fromMappingId, initiatedByMappingId**, these refer to the IDs of the mappings. IDs can either be reserved IDs (see [AddressMappings](address-mappings-lists.md)) or IDs of mappings created through MsgCreateAddressMappings.
* **transferTimes**: When can the transfer takes place? A [UintRange](uint-ranges.md)\[] of times (UNIX milliseconds).
* **badgeIds**: What badge IDs can be transferred? A [UintRange](uint-ranges.md)\[] of badge IDs.
* **ownershipTimes**: What ownership times for the badges are being transferred?

For example, we might have something like  the following:

* `(Mint, alice, bob, [1-10], [1-2], [1000-2000]) -> APPROVED`
* `(AllWithoutMint, alice, All [1-10], [1-2], [1-1000]) -> DISAPPROVED`

Let's break down the first bullet above.

* The "Mint" mapping ID corresponds to the Mint address. This approval only allows transfers from the "Mint" address.
* Likewise, the "alice" mapping ID and "bob" mapping IDs correspond to alice and bob's addresses. The approval allows transfers to alice and initiated by Bob.
* The ownership rights for the times 1-1000 of badge IDs 1-2 can be transferred from time 1 to time 10.&#x20;

Note the approval only applies to the details defined and must match ALL details. Badge ID #3 is not defined by this approval even if all other criteria matches.

#### Options - Manipulating the Main Fields

Each of the main fields has a corresponding options field. This is just for shorthand representation to manipulate and override the default field value. For example, you could do something like this:

```json
{
    badgeIds: [{ start: 1, end: 10}],
    badgeIdsOptions: { invertDefault: true, allValues: false, noValues: false }
}
```

The result of this would be badges 11+.

This is also available and convenient for address mappings.

```json
{
    toMappingId: "Mint",
    toMappingOptions: { invertDefault: true }
}
```

This would represent the mapping for all addresses EXCEPT the mint address. Note that we also reserve the "!" prefix for inverting a given mapping ID and reserve the "AllWithoutMint" and "AllWIthMint" mapping IDs (see [Address Mappings](address-mappings-lists.md)).

#### Approval Criteria

The **`approvalCriteria`** section corresponds to additional restrictions or challenges necessary to be satisfied for approval. It defines aspects like the quantity approved, maximum transfers, and more. There is a lot here, so we have dedicated a page to just explaining the [approval details here](approval-options.md).&#x20;

For the rest of this page, you can simply think of it as the challenges or restrictions that need to be obeyed to be approved (in addition to having **isApproved** = true).&#x20;

The **approvalTrackerId** and **challengeTrackerId** correspond to **approvalCriteria** and are explained on the following page as well.

**Breaking Down Range Logic**

At execution time, even though the interface uses range logic (UintRanges, AddressMappings), we break everything down into single value tuples, such as (bob, alice, bob, 1, 1, 1000) and check each singular value tuple separately. See example further down below.

#### Matching Transfers to Approvals

The process of matching transfers to approvals involves several steps:

1. Apply all manipulations (options) to each approval while maintaining order.&#x20;
2. Expand all approval tuples  to singular tuple values (e.g. (bob, alice, bob, badge ID #1, ....)
3. Expand the current transfer tuple to singular tuple values.
4. Find all matches.&#x20;
   1. If anything is disapproved or unhandled on any level, the overall transfer is disapproved.
   2. Find a corresponding approval for all (first match by default but can be customized with  **prioritizedApprovals** and **onlyCheckPrioritizedApprovals**).
   3. In the case of overflowing approvals (e.g. we are transferring x10 but have two approvals for x3 and x12), we deduct as much as possible from each one as we iterate. So using the previous example, we would end up with x3/3 of the first approval used and x7/12 of the second used.&#x20;
5. Lastly, we check the **`approvalCriteria`** for each one-to-one match and ensure it satisfies all criteria.

See example below.

### Example

Let's consider a scenario:

* Transfer: `(bob, alice, bob, 10, 1-2, 10-100)`
* Approved Transfers:
  * `(bob, alice, bob, 10, [1-2], [1000-2000]) -> (true, approvalCriteria)`
  * `(bob, alice, bob, 10, [1-2], [10-50]) -> (true, approvalCriteria)`
  * `(bob, alice, bob, 10, [1-2], [51-100]) -> (false, approvalCriteria)`
  * `(bob, alice, bob, 10, [1-2], [10-100]) -> (true, approvalCriteria)`

In this case, using first matches, the transfer would be approved for `(bob, alice, bob, 10, [1-2], [10-50])`, provided it adheres to `approvalCriteria`. However, it wouldn't be approved for `(bob, alice, bob, 10, [1-2], [51-100])` due to incompatible owned times. Thus, overall, the transfer would be DISAPPROVED.

See [tutorials ](../learn-the-interface/)for further examples.

### **Difference From UpdateApprovedTransferPermission**

While this may seem similar to the UpdateApprovedTransferPermission, the permission corresponds to the **updatability** of the approved transfers.

The approved transfers themselves correspond to if a transfer is currently approved or not.

### Conclusion

To grasp the concepts further, additional examples and resources can be referenced in the "Learn the Interface" section.

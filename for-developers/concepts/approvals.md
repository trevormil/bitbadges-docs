# 🤝 Approvals

First, read [Transferability ](../../overview/concepts/transferability.md)for an overview of approved transfers.

Note: The [Approved Transfers](approvals.md) and [Permissions ](../../overview/concepts/manager.md)are the most powerful features of the interface, but they can also be the most confusing. Please ask for help if needed. For further examples, please reference the [Learn the Interface](../interface-examples.md) section.

## Approved Transfers Overview

### Approval Levels - Collection, Incoming, Outgoing

Approved transfers encompass three hierarchical levels: collection, incoming, and outgoing, as previously elaborated. The interfaces for these three levels share common elements, with slight variations in functionality:

* Incoming approvals exclude the "to" fields as they are automatically populated with the recipient's address.
* Outgoing approvals omit the "from" fields, as they are automatically filled with the sender's address.
* The collection level holds the capacity to override user-level (incoming / outgoing) approvals, but not vice versa.

**For a transfer to be approved overall, it has to satisfy the collection-level approvals, and if not overriden forcefully by the collection-level approvals, the user incoming / outgoing also have to be satisfied.**

### Approvals != Escrows

When it comes to approved transfers, it's important to note that they are just approvals and may not necessarily correspond directly to underlying balances. Approvers must ensure that sufficient balances are available to uphold the approvals' integrity, accounting for potential revokes or freezes. This responsibility extends to the collection-level transferability. such as ensuring adequate balances for transfers from the "Mint" address.

On a similar note, if approvals become no longer valid (such as approving a badge but it was revoked via a different approval), the former approval doesn't automatically get cancelled. It is the approver's responsibility to handle them accordingly.

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

Note that user incoming / outgoing approvals follow the same interface except **toMapping** is auto-populated with the user's address for incoming approvals and similarly the **fromMapping** for outgoingApprovals.

#### Approvals vs Disapprovals

We allow you to create approvals and disapprovals based on whether **isApproved** = true or false. If you are creating a disapproval (**isApproved** = false), the approval fields like **approvalCriteria**, etc will be ignored, and you can leave empty or default.

**Approved vs Unapproved**

Approvals are simply a set of criteria, so it is entirely possible the same transfer can map to multiple approvals (or disapprovals) on the same level. We handle approvals per level in the following manner:

1. If the transfer matches to ANY disapproval, the whole transfer is considered disallowed, regardless if it matches with any valid approval(s).
2. If the transfer is unhandled (doesn't match to any approval or disapproval), it is DISAPPROVED by default.
3. If the transfer matches to multiple approvals, we take first-match (linear scan) by default. However, we allow the user to specify **prioritizedApprovals** and **onlyCheckPrioritizedApprovals** (in MsgTransferBadges) when transferring, so they can only use up their desired approvals in rare cases.

We recommend designing approvals in a way where all are mutually exclusive, meaning no transfer can map to multiple. This improves the simplicity and readability of your collection, and you will never need **prioritizedApprovals** or **onlyCheckPrioritizedApprovals.**

#### Approval ID

All approvals where **isApproved** = true must have a unique **approvalId** for identification per level. The **approvalTrackerId** and **challengeTrackerId** are different, and we will explain those on the following page along with **approvalCriteria**.

**Metadata**

We provide an optional **uri** and **customData** to allow you to add a link to something about your approval. We do not use it on the site but it is provided if needed.

**Who? When? What? - Main Fields**

To represent transfers, six main fields are used: **`toMapping`**, **`fromMapping`**, **`initiatedByMapping`**, **`transferTimes`**, **`badgeIds`**, and **`ownershipTim`**`es`. These fields collectively define the transfer details, such as the addresses involved, timing, and badge details. This representation leverages range logic, breaking down into individual tuples for enhanced comprehension.

* **toMapping, fromMapping, initiatedByMapping**: [AddressMappings](address-mappings-lists.md) specifying which addresses can send, receive, and initiate the transfer. If we use **toMappingId, fromMappingId, initiatedByMappingId**, these refer to the IDs of the mappings. IDs can either be reserved IDs (see [AddressMappings](address-mappings-lists.md)) or IDs of mappings created through [MsgCreateAddressMappings](cosmos-msgs.md).
* **transferTimes**: When can the transfer takes place? A [UintRange](uint-ranges.md)\[] of times (UNIX milliseconds).
* **badgeIds**: What badge IDs can be transferred? A [UintRange](uint-ranges.md)\[] of badge IDs.
* **ownershipTimes**: What ownership times for the badges are being transferred?

For example, we might have something like  the following:

* ```json
  "fromMappingId": "Mint", //reserved mapping ID for the "Mint" addres
  "toMappingId": "AllWithoutMint", //reserved mapping ID for all addresses (excluding "Mint")
  "initiatedByMappingId": "AllWithoutMint",
  "transferTimes": [
    {
      "start": "1691931600000",
      "end": "1723554000000"
    }
  ],
  "ownershipTimes": [
    {
      "start": "1",
      "end": "18446744073709551615" //Max possible value
    }
  ],
  "badgeIds": [
    {
      "start": "1",
      "end": "100"
    }
  ],
  ```

Let's break down the definition above.

* The "Mint" mapping ID corresponds to the Mint address. This approval only allows transfers from the "Mint" address. The transfer can be initiated by any user (because the AddressMapping "AllWithoutMint" includes all addresses).
* The ownership rights for any time of badge IDs 1-100 can be transferred from UNIX time 1691931600000 (Aug 13, 2023) to time 1723554000000 (Aug 13, 2024).&#x20;

Note the approval only applies to the details defined and must match ALL details. For example, badge ID #101 is not defined by this approval even if all other criteria matches.

**Transferring From Mint Address**

As mentioned before, we check the collection level approvals first, and if not overriden, we check the user-level incoming/ outgoing approvals.

The Mint address has its own approvals, but since it is not a real address, they are always empty. Thus, it is important that when you attempt transfers from the Mint address, you override the outgoing approvals of the Mint address (see [Overrides](approval-criteria.md#overrides) on the next page for how).

It is recommended that when dealing with approvals from the "Mint" address, the approval's **fromMapping** is just the "Mint" address and no other address. This helps readability and simplicity and avoiding unintentionally approving users to mint. See Example 2 below.

#### Options - Manipulating the Main Fields

Each of the main fields has a corresponding options field (badgeIds -> badgeIdsOptions). This is just for shorthand representation to manipulate and override the default field value. For example, you could do something like this:

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

This would represent the mapping for all addresses EXCEPT the mint address.&#x20;

Note that we also reserve the "!" prefix for inverting a given mapping ID and reserve the "AllWithoutMint" and "AllWIthMint" mapping IDs (see [Address Mappings](address-mappings-lists.md)).

#### Approval Criteria

The **`approvalCriteria`** section corresponds to additional restrictions or challenges necessary to be satisfied for approval. It defines aspects like the quantity approved, maximum transfers, and more. There is a lot here, so we have dedicated a page to just explaining the [approval details here](approval-criteria.md).&#x20;

For the rest of this page, you can simply think of it as the challenges or restrictions that need to be obeyed to be approved (in addition to having **isApproved** = true).&#x20;

The **approvalTrackerId** and **challengeTrackerId** correspond to **approvalCriteria** and are explained on the following page as well.

**Breaking Down Range Logic**

Even though our interface uses range logic (UintRanges, AddressMappings), we break everything down into single-value tuples (e.g., `(bob, alice, bob, 1, 1, 1000)`) and check each singular value tuple separately. This simplifies the matching process and enhances clarity.

#### Matching Transfers to Approvals

The process of matching transfers to approvals involves several steps. This is done on a per-level basis.

1. We start with the collection-level approvals.
2. Apply all manipulations (options) to each approval while maintaining order.&#x20;
3. Expand all approval tuples with range logic (AllWithMint, ...., \[1-100])  to singular tuple values (e.g. (bob, alice, bob, badge ID #1, ....)
4. Expand the current transfer tuple to singular tuple values.
5. Find all matches.&#x20;
   1. If anything is disapproved or unhandled on any approval level (accounting for overrides), the overall transfer is disapproved.
   2. Find corresponding approvals for all (first match by default but can be customized with  **prioritizedApprovals** and **onlyCheckPrioritizedApprovals**).
   3. In the case of overflowing approvals (e.g. we are transferring x10 but have two approvals for x3 and x12), we deduct as much as possible from each one as we iterate. So using the previous example, we would end up with x3/3 of the first approval used and x7/12 of the second used.&#x20;
6. Lastly, we check the **`approvalCriteria`** for each one-to-one match and ensure everything is satisfied.
7. For any amounts / balances that were approved but do not override incoming / outgoing approvals respectively, we go back to step 2 and check the recipient's incoming approvals and the senders' outgoing approvals.

### Genesis Values and Default Approvals

For incoming and outgoing approvals, the collection can define default values for each user's approvals via **`defaultIncomingApprovals`** and **`defaultOutgoingApprovals`**. These defaults are applied when initializing a balance for the first time in an account.

Additionally, we automatically add an unlimited approval (with no amount restrictions) in the following cases:

a) User's outgoing approvals when the sender is the same as the initiator.&#x20;

b) User's incoming approvals when the recipient is the same as the initiator.



In order to allow forceful transfers to an address without prior approval, the defaultIncomingApprovals must be set to something like below. Otherwise, if empty or \[], then all transfers must be initiated by or manually approved by the recipient by default (opt-in only).

```json
"defaultIncomingApprovals": [
    {
      "fromMappingId": "AllWithMint",
      "initiatedByMappingId": "AllWithMint",
      "transferTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ],
      "badgeIds": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ],
      "ownershipTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ],
      "approvalId": "forceful-transfers-allowed",
      "approvalTrackerId": "",
      "challengeTrackerId": "",
      "isApproved": true
    }
  ]
```

### **Difference From Permission**

While this may seem similar to the UpdateApprovedTransferPermission, the permission corresponds to the **updatability** of the approved transfers.

The approved transfers themselves correspond to if a transfer is currently approved or not.

### **Example 1 - Putting It Together**

Bob is transferring x10 of **ownershipTimes** 1-Max of **badgeIds** 1-2 at time T to Alice.&#x20;

#### Transfer Request:

Transfer: `(Bob, Alice, Bob, T, [1-2], [1-Max])`

#### Collection Approved Transfers:

1. `(Bob, Alice, Bob, T, [1-2], [1000-2000]) -> APPROVED`
2. `(Bob, Alice, All, T, [1-2], [1-2000]) -> APPROVED`
3. `(Bob, Alice, All, T, [1-2], [2001-Max]) -> APPROVED`

Let's say each approval has no amount restriction but does not override the user level incoming / outgoing approvals.&#x20;

In this scenario, let's say the default "first match" approach is used:

1. The first approved transfer `(Bob, Alice, Bob, T, [1-2], [1000-2000])` matches the transfer request partially, but it only covers **ownershipTimes** from 1000 to 2000. We deduct this overlap but still have a lot remaining to be approved for (\[1-999], \[2001-Max]).
2. The second approved transfer `(Bob, Alice, All, T, [1-2], [1-2000]),` again partially matches, and we deduct. This partial match only handles 1-999 because we handled 1000-2000 already. Note that this approval says it can be initiated by "All" instead of Bob, but Bob's address is within the "All" mapping.
3. The third approved transfer `(Bob, Alice, All, T, [1-2], [1001-Max])` covers the rest. This transfer is approved on the collection level because the entire transfer was handled.

If we hypothetically had a fourth approved transfer `(Bob, Alice, Bob, T, [1-2], [1-1]) -> DISAPPROVED,` the transfer would fail because it matches to a disapproval.

Or, if Bob was requesting badge ID 3 to be transferred as well, it would fail because badge ID 3 is unhandled by all defined approvals (and disallowed by default if unhandled).

**Outgoing Approvals**

Because we did not override the user level approvals, we need to check that Bob approved this transfer in his approvals.

The process above would then be repeated for Bob's outgoing approvals. In this case, Bob is the initiator, so we automatically add an unlimited approval by default (see below).

**Incoming Approvals**

Likewise, we also need to check Alice's incoming approvals using the same process.&#x20;

**Satisfied?**

If all levels are satisfied, the transfer is approved, and we deduct/increment the used approvals where necessary. See [tutorials ](../interface-examples.md)for further examples.

**Extending the Example: Prioritized Approvals**

Let's say that Bob only wants to use the second and third approvals from the collection level but not the first. By default, a first-match policy is applied, so it would by default use the first one as shown above.

When initiating the transfer (MsgTransferBadges), Bob can set **prioritizedApprovals** to be the second and third collection approvals. These would then be checked first, followed by the first approval.&#x20;

If Bob additionally sets **onlyCheckPrioritizedApprovals** = true, we only check the ones specified in **prioritizedApprovals**.

### Example 2 - Collection

This would define a collection where badges 1-100 can be transferred from the Mint address (according to the first approval). Once transferred out of the Mint address, they can be transferred freely, thus making the collection transferable.

Note how the **fromMapping** of each approval are non-overlapping, so any transfer will only match to one of the two approvals (if either). The first approval is restricted to transfers from the Mint address whereas the second is all EXCEPT the Mint address.

```json
 "collectionApprovals": [
    {
      "fromMappingId": "Mint",
      "toMappingId": "AllWithMint",
      "initiatedByMappingId": "AllWithMint",
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
      "isApproved": true,
      "approvalId": "claim-from-mint-address",
      ... //other criteria (including the IMPORTANT overrideFromOutgoingApprovals = true since we are dealing with transfers from the Mint address)
    },
    {
      "fromMappingId": "AllWithoutMint",
      "toMappingId": "AllWithoutMint",
      "initiatedByMappingId": "AllWithoutMint",
      "badgeIds": [
        {
          "start": "1",
          "end": "100"
        }
      ],
      "ownershipTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ],
      "transferTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ],
      "approvalId": "transferable",
      "isApproved": true
    }
  ],
```

### Example 3 - Outgoing Approvals

This would set approve Charlie to send badges to Bob on this user's behalf.

```json
"outgoingApprovals": [
  {
    "toMappingId": "Bob",
    "initiatedByMappingId": "Charlie",
    "transferTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "badgeIds": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "ownershipTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "approvalId": "test",
    "approvalTrackerId": "asdfasdf",
    "challengeTrackerId": "",
    "isApproved": true
    //see next page
    "approvalCriteria": //define approval criteria (how much? challenges? etc here)
  }
]
```

### Example 4 - Incoming Approvals

This would set approve this user to receive any transfer from Bob.&#x20;

```json
"incomingApprovals": [
  {
    "fromMappingId": "Bob",
    "initiatedByMappingId": "AllWithMint",
    "transferTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "badgeIds": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "ownershipTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "approvalId": "test",
    "approvalTrackerId": "asdfasdf",
    "challengeTrackerId": "",
    "isApproved": true
  }
]
```


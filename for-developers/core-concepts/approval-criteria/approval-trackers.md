# Approval Trackers

```typescript
export interface CollectionApproval<T extends NumberType> {
  amountTrackerId: string;
  approvalId: string
  ...
}
```

## Approval Trackers

Approval or amount trackers track how many badges have been transferred and how many transfers have occurred. This is done via an incrementing tally system with a threshold.&#x20;

Take the following approval tracker

1. You are approved for x10 of badge IDs 1-10. For simplicity, lets say the tracker ID is "xyz".
2. You transfer x5 of badge IDs 1-10 -> "xyz" tally goes from x0/10 -> x5/10
3. You transfer another x5 -> "xyz" tally goes to x10/10
4. You transfer another x1 -> exceed threshold so transfer fails and will also fail for all subsequent approvals that match to "xyz".
5. However, if subsequent transfers match to tracker "abc", the tally starts from zero again because it is a different tracker w/o any history.

### **How are approval trackers identified?**

Above, we used "xyz" for simplicity, but the identifier of each approval tracker actually consists of the **amountTrackerId** along with other identifying details. For simplicity, we recommend keeping the **amountTrackerId** the same as the **approvalId,** unless implementing advanced functionality. The **amountTrackerId** cannot be the same as a different approval's **approvalId.**

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

The **trackerType** corresponds to what type of tracker it is.

If "overall", this is applicable to any transfer and will increment everytime the approval is used. This creates a single universal tally. **approvedAddress** will be empty.&#x20;

If "to", "from", or "initiatedBy", the **approvedAddress** is the sender, recipient, or initiator of the transfer, respectively. Note since **approvedAddress** and **trackerType** are part of the approval tracker's ID, this creates unique individual tallies per address per tracker type.

For example, these correspond to different trackers because the **approvedAddress** is different.

`1-collection- -uniqueID-initiatedBy-alice`

`1-collection- -uniqueID-initiatedBy-bob`

**Handling Multiple Trackers**

Trackers are ID-based, and thus, multiple trackers can be created. Take note of what makes up the ID. The collection ID, approval level, approver address, and more are all considered. If one changes or is different, the whole ID is different and will correspond to a new tracker.

### **What is tracked?**

```typescript
export interface ApprovalTrackerInfoBase<T extends NumberType> extends ApprovalTrackerIdDetails<T> {
  numTransfers: T;
  amounts: Balance<T>[];
}
```

Each transfer that maps to the tracker increments **numTransfers** by 1, and each badge transferred increments the **amounts** in the interface.

Example:

`ID: 1-collection- -uniqueID-initiatedBy-alice`

```json
{
    "numTransfers": 10,
    "amounts": [{ 
        "amount": 10n, 
        "badgeIds": [{ start: 1n, end: 1n }], 
        "ownershipTimes":  [{ start: 1n, end: 100000000000n }], 
    }]
}
```

**As-Needed Basis**

We increment on an as-needed basis. Meaning, if there is no need to increment the tally (unlimited limit and/or not restrictions), we do not increment for efficiency purposes. For example, if we only are tracking **numTransfers** but do not need the **amounts**, we do not increment the amounts.

Edge case: Note that sometimes, in [Predetermined Balances](approval-trackers.md#predetermined-balances), if you specify useOverallNumTransfers, usePerToAddressNumTransfers, usePerFromAddressNumTransfers, or usePerInitiatedByAddressNumTransfers, we do need to track the number of transfers and do increment the corresponding tracker, even if the corresponding maximum number of transfers for that level is not set / set to unlimited.

**Increment Only**

Trackers are increment only and immutable in storage. To start an approval tally from scratch, you will need to map the approval to a new unused ID. This can be done simply by editing **amountTrackerId** or **challengeTrackerId** (because this changes the whole ID) or restructuring to change one of the other fields that make up the overall ID.

IMPORTANT: Because of the immutable nature, be careful to not revert to a previously used ID unintentionally because the starting point will be the previous tally (not starting from scratch).

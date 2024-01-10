# Linking Trackers (Advanced)

```typescript
export interface CollectionApproval<T extends NumberType> {
  amountTrackerId: string;
  challengeTrackerId: string;
  ...
}
```

As explained, we have two separate tracker types: **amountTrackerId** for tracking the amounts and number of transfers and **challengeTrackerId** for tracking used leaves of Merkle challenges.

For most approvals, trackers are to be limited in scope to a single approval only at a time. However, in some instances, you may want to link tracker IDs. This can allow you to implement OR or XOR logic between different approvals / challenges on the same level. This means you can increment the SAME tracker when DIFFERENT approvals are used.&#x20;

It is important to note though that identifying a tracker is not simply just the amountTrackerId or challengeTrackerId. It is a multi-faceted ID w/ 5 or 6 potential variables. To increment the same tracker, all identifying details have to match.

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

```typescript
{
  collectionId: T;
  approvalLevel: "collection" | "incoming" | "outgoing";
  approverAddress: string;
  challengeTrackerId: string;
  leafIndex: T;
}
```

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

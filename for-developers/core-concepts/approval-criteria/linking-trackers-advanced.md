# Cross-Approval Logic (Advanced)

```typescript
export interface CollectionApproval<T extends NumberType> {
  approvalId: string
  amountTrackerId: string;
  challengeTrackerId: string;
  ...
}
```

As explained, we have two separate tracker types: **amountTrackerId** for tracking the amounts and number of transfers and **challengeTrackerId** for tracking used leaves of Merkle challenges.

For most approvals, trackers are to be limited in scope to a single approval only at a time. This is achieved if **amountTrackerId = challengeTrackerId = approvalId.**

However, in some instances, you may want to link tracker IDs. This can allow you to implement OR or XOR logic between different approvals / challenges on the same level. This means you can increment the SAME tracker when DIFFERENT approvals are used. For example, you may want to prevent double dipping between two approvals.

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

For example, the following pseudocode would allow address C (in both whitelist ABC and CDE) to claim a max of x10 because the tallys are linked (same tracker ID).

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

## Handling

It is important to handle this correctly, as intended. Please make sure you understand exactly how trackers work behind the scenes. They are complex and can have many moving parts, so consider everything.

**Permissions**

You also will need to handle the approval update permissions accordingly.&#x20;

Let's explain via a scenario. Let's say you create an approval that tracks used Merkle challenge leaves with a one use per leaf limit. By default, due to the way **challengeTrackerId** works (if not equal to its **approvalId**), multiple approvals can have the same **challengeTrackerId.**

Let's say you create an approval for badges 1-100 with **challengeTrackerId** = "abc", and you lock updating any approval for badges 1-100 but don't specify anything about the **challengeTrackerId**.

While you cannot create a new approval for badges 1-100, you can create a new approval for badge IDs 101+ with the same **challengeTrackerId** = "abc". In storage, the challenges are inherently linked, so when leaf #5 is used up in the new 101+ approval, leaf #5 will also be marked as used in the 1-100 approval. This totally messes up how the first approval operates.

So, to ensure a **challengeTrackerId** and/or **approvalTrackerId** is never updated / used again, you must brute force ALL combinations with that specific ID.

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "fromListId": "AllWithMint",
  "toListId": "AllWithMint",
  "badgeIds" [{ 1-Max }],
  ...
  "approvalId": "All",
  "amountTrackerId": "All",
  "challengeTrackerId": "xyz", //enforces any approval with xyz cannot be edited
  "permanentlyPermittedTimes": [],
  "permanentlyForbiddenTimes": [
    {
      "start": "1",
      "end": "18446744073709551615"
    }
  ]
}
</code></pre>



Also, note that simply locking a specific **approvalId** is not adequate for approvals that use tracker IDs (**challengeTrackerId** and/or **approvalTrackerId**) not equal to the approval ID. For example, the following would restrict that approval "xyz" cannot be edited, but a different approval could be created with the same tracker IDs and mess up the behavior of approval "xyz" because the tracker can be incremented in undesired ways.

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "fromListId": "AllWithMint",
  "toListId": "AllWithMint",
  "badgeIds" [{ 1-Max }],
  ...
  "approvalId": "xyz", //enforces the approval with ID xyz cannot be edited
  "amountTrackerId": "All",
  "challengeTrackerId": "All", 
  "permanentlyPermittedTimes": [],
  "permanentlyForbiddenTimes": [
    {
      "start": "1",
      "end": "18446744073709551615"
    }
  ]
}
</code></pre>


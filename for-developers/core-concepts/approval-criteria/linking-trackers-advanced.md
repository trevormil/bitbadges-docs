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

<pre class="language-json"><code class="lang-json"><strong>[{
</strong>    Tracker ID: 123
    Approve x5 of IDs 1-10 if you are on whitelist ABC,
}, {
    Tracker ID: 456,
    Approve x10 of IDs 1-10 if you are on whitelist CDE
}]
</code></pre>

## Handling

It is important to handle this correctly, as intended. Please make sure you understand exactly how trackers work behind the scenes. They are complex and can have many moving parts, so consider everything.

### Permissions

Note that cross-approval logic also makes permissions require extra checks to truly forbid / freeze / permit updating an approval.&#x20;

First, its important to distinguish that with the way trackers work, an approval's expected behavior can be affected by another approval, even if the former approval was never updated at all.

Let's explain via a scenario. Let's say you create an approval that tracks used Merkle challenge leaves with a one use per leaf limit. By default, due to the way **challengeTrackerId** works (if not equal to its **approvalId**), multiple approvals can have the same **challengeTrackerId.**

Let's say you create an approval for badges 1-100 with **challengeTrackerId** = "abc", and you lock updating any approval for badges 1-100 but don't specify anything about the **challengeTrackerId**.

While you cannot create a new approval for badges 1-100, you can create a new approval for badge IDs 101+ with the same **challengeTrackerId** = "abc". In storage, the challenges are inherently linked, so when leaf #5 is used up in the new 101+ approval, leaf #5 will also be marked as used in the 1-100 approval. This totally messes up how the first approval operates.

Now, lets establish some definitions.&#x20;

* Non-Updatable: Values are considered non-updatable according to the permissions if they brute force ALL possible combinations for that values. For example, the following permission brute forces badge IDs 2-10. Non-updatability means that approvals with IDs 2-10 can never be created again, but this doesn't completely rule out such approvals behavior being affected by cross-approval logic.

```json
{
  "fromListId": "AllWithMint",
  "toListId": "AllWithMint",
  "initiatedByListId": "AllWithMint",
  "badgeIds":  [{ "start": "2", "end": "10" }],
  "transferTimes":  [{ "start": "1", "end": "18446744073709551615" }],
  "ownershipTimes":  [{ "start": "1", "end": "18446744073709551615" }],
  "approvalId": "All", //forbids approval "xyz" from being updated
  "amountTrackerId": "All",
  "challengeTrackerId": "All",
  "permanentlyPermittedTimes": [],
  "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }]
}
```

* An approval is non-updatable AND will always keep its expected behavior if BOTH of the following are true
  * ONE of its values is COMPLETELY non-updatable (for an approval of badge IDs 2-10, the above permission would satisfy this, but if it was for 2-11, it would not)
    * This can be satisfied for any value (from, to, IDs, times, tracker IDs, ...)
  * No cross-approval logic will EVER affect it (see below)



* No cross-approval logic will ever affect an approval if 1+ of the following is satisfied
  * If ONE of its values is COMPLETELY non-updatable AND (**amountTrackerId** = **approvalId** = **challengeTrackerId)**
    * All logic is scoped to the current approval
  * If ONE of its values is COMPLETELY non-updatable AND it doesn't use trackers at all
    * if it currently doesn't use trackers, nothing another approval can do can affect its behavior
    * note if it doesn't use trackers, it is best practice to make (**amountTrackerId** = **approvalId** = **challengeTrackerId)**&#x20;
  * Both the tracker IDs are non-updatable via the permissions, which would make all approvals using these as non-updatable and all cross-approval logic would be set in stone
  * <pre class="language-json"><code class="lang-json"><strong>{
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

**Freezing Specific Values**

Specific values are considered non-updatable AND keep the same behavior if:

1. The values themselves are brute forced to be non-updatable
2. All approvals that contain the value in some way (even if partially overlap) are non-updatable AND keep the same expected behavior (not prone to cross-approval logic attacks)



So, if you want to freeze specific values, you need to do the following:

1. Brute force the value and make it non-updatable
2. For each approval that would be affected by it in some way (even if only partially overlaps)
   1. Using the new permissions, is it non-updatable AND not prone to cross-approval logic? If so, we do not have to do anything.
   2. If it is updatable OR prone to cross-approval logic in some way, you need to handle it.&#x20;
      1. The most straightforward way is to make both tracker IDs non-updatable. This will handle the matching approval, but note that it may also affect other approvals. It is up to you whether you want to handle it recursively, or leave it here. If you handle it recursively, note that both tracker IDs need to be satisfied.
      2. You could also play around with it and see if you only need to freeze one value like the approvalId. The result just needs to be non-updatable AND not prone to cross-approval logic in some way.


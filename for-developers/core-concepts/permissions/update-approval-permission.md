# Update Approval Permission

Pre-Readings: [Transferability](../transferability-approvals.md) and [Approval Criteria](../approval-criteria/)

The ApprovalPermissions refer to the UPDATABILITY of the currently set approvals / transferability. These can be leveraged to freeze specific transferability / approvals in order to give users more confidence that they cannot be changed in the future. Note this refers to the updatability of them and has no bearing on what they are currently set to.

For what transfer combinations (see [Representing Transfers](../transferability-approvals.md)), can I create / delete / update approvals?&#x20;

```json
"userPermissions": {
    "canUpdateIncomingApprovals": [...],
    "canUpdateOutgoingApprovals": [...],
    ...
}
```

```json
"collectionPermissions": {
    "canUpdateCollectionApprovals": [...]
    ...
}
```

The **canUpdateIncomingApprovals** and **canUpdateOutgoingApprovals** follow the same interface as **canUpdateCollectionApprovals** minus automatically populating the user's address for to / from for incoming / outgoing, respectively. We only explain the collection approval permission to avoid repetition.

```typescript
export interface CollectionApprovalPermission<T extends NumberType> {
  fromListId: string;
  toListId: string;
  initiatedByListId: string;
  transferTimes: UintRange<T>[];
  badgeIds: UintRange<T>[];
  ownershipTimes: UintRange<T>[];
  approvalId: string
  approvalTrackerId: string
  challengeTrackerId: string
  
  permanentlyPermittedTimes: UintRange<T>[];
  permanentlyForbiddenTimes: UintRange<T>[];
}
```

Ex: I can/cannot update the approvals for the transfer combinations ("All", "All", "All", 1-100, 1-10, 1-10, "All",  "All",  "All") tuple.

## ID Shorthands

IDs are used for locking specific approvals or trackers. This is because sometimes it may not be sufficient to just lock a specific (from, to, initiator, time, badge IDs, ownershipTimes) combination because multiple approvals could match to it.

To specify IDs, you can use the "All" reserved ID to represent all IDs, or you can use other shorthand methods such as "!xyz" to denote all IDs but xyz. These shorthands are the same as reserved lists, so we refer you[ there for more info](../address-lists-lists.md). Just replace the addresses with the IDs.

## Break Down Logic

Because of the way breakdown logic is performed, the following is allowed.

Permission: Badge IDs 2-10 are locked as forbidden

Before: 1-10 -> Criteria ABC

After: 1 -> Criteria XYZ, 2-10 -> Criteria ABC

## Expected Behavior vs Non-Updatability

Approval permissions are slightly tricky because even though an approval may be non-updatable according to the permissions set, its expected behavior may change due to how they are designed (i.e. using trackers).

* For example, lets say we want to freeze IDs 501-1000 and have an incrementing mint of x1 of ID 1, x1 of ID 2, up to ID 1000. If we simply freeze the IDs 501-1000, the approval could still be deleted for IDs 1-500, and the increment number (tracker) will then never reach 501 because it will be out of bounds every time. Thus, expected behavior for 501-1000 changes even though it is frozen.
* Or, let's say you create an approval for badges 1-100 with **challengeTrackerId** = "abc" and **approvalId** = "xyz", and you lock updating any approval for badges 1-100 but don't specify anything about the **challengeTrackerId**. While you cannot create/edit a new approval for badges 1-100, you can create a new approval for badge IDs 101+ with the same **challengeTrackerId** = "abc". In storage, the trackers are inherently linked, so when leaf #5 is used up in the new 101+ approval, leaf #5 will also be marked as used in the 1-100 approval. This totally messes up how the first approval operates.

### Definitions

To explain things easier, let's start with some definitions:

#### **Approval Tuple**

An approval tuple is a set of values (**from, to, initiatedByList, badgeIds, transferTimes, ownershipTimes, approvalId, challengeTrackerId, amountTrackerId**).&#x20;

Note that to match to an approval tuple, all N criteria must match. If one doesn't, it isn't a match. For example, in the example below, badge ID 1 will never match to the tuple.

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "fromListId": "AllWithMint",
  "toListId": "AllWithMint",
  "initiatedByListId": "AllWithMint",
  "badgeIds":  [{ "start": "2", "end": "10" }],
  "transferTimes":  [{ "start": "1", "end": "18446744073709551615" }],
  "ownershipTimes":  [{ "start": "1", "end": "18446744073709551615" }],
  "approvalId": "All", //forbids approval "xyz" from being updated
  "amountTrackerId": "All",
  "challengeTrackerId": "All"
}
</code></pre>

#### **Non-Updatable**

For a given approval tuple, it is considered non-updatable according to the permissions if all possible combinations of the entire tuple is **permanently forbidden** in the permissions.&#x20;

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "fromListId": "AllWithMint",
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
</code></pre>

Note that non-updatability is scoped to the tuple itself. Ex:

Permission: Badge IDs 2-10 are locked as forbidden

Before Approval: 1-10 -> Criteria ABC

After Approvals: 1 -> Criteria XYZ, 2-10 -> Criteria ABC

**Brute Forced**

Commonly, you will make some values non-updatable by specifying some criteria (e.g. IDs 2-10) and setting everything else to all possible values. For example, the permission above does this for badge IDs 2-10. We refer to this as brute forcing (i.e. above badge IDs 2-10 are brute forced but badge IDs 2-11 are not).&#x20;

In other words, for some criteria, all possible combinations of that criteria are COMPLETELY forbidden and non-updatable.

#### **Expected Behavior**

As explained above, expected behavior not only encompasses non-updatability, but it also makes sure that nothing any other approval or update can do can affect the expected behavior of this approval. This designation is especially important.

### Forbidding Specific Approval Tuple Updates

With the way trackers work, it is important to handle approval permissions correctly to protect against cross-approval logic or break-down attacks.&#x20;

Below, we will walk through the process of making a specific approval tuple non-updatable AND keeping its expected behavior. If you want to do this a specific approval that is set, the approval tuple should consist of the speciifc values for that specific approval.

**Algorithm**

Given an approval tuple, do the following:

1. Set the tuple to be non-updatable in the permissions
2. Find all approvals currently set that overlap (even partially) with the tuple. For all matches, do the following:
   1. Handle Challenges&#x20;
      1. Does the approval rely on challenge trackers (i.e. **merkleChallenge** is defined)? If not, you are good. If it doesn't currently depend on it, it cannot be updated once the permission is set.
      2. Is the approval non-updatable (use the updated permissions with the tuple non-updatable) and **approvalId = challengeTrackerId**? If so, you know that the challenge logic is always scoped and will behave as expected.
      3. Is **challengeTrackerId** brute forced in permissions? If so, you know that the challenge logic will always be as expected.
   2. Handle Amount Trackers
      1. Does the approval rely on amount trackers? If not, you are good. If it doesn't currently depend on it, it cannot be updated once the permission is set.
         * Relying on amount trackers means that it relies or tracks values for **approvalAmounts, predeterminedBalances**, or **maxNumTransfers.**  There is an edge case where the approval relies on **predeterminedBalances** but only for challenges (**orderCalculationMethod**.**useMerkleChallengeLeafIndex**). This is fine as long as it doesn't rely on the other two.
      2. Is the approval non-updatable (use the updated permissions with the tuple non-updatable) and **approvalId = amountTrackerId**? If so, you know that the logic is always scoped and will behave as expected.
      3. Is **amountTrackerId** brute forced in permissions? If so, you know that the logic will always be as expected.
   3. Handle&#x20;









1. AND for all overlapping approvals currently set, one of the following is satisfied:
   1. The approval does not use trackers altogether. If the approval currently doesn't, they cannot be updated to bc permission is forbidden.
   2. Uses trackers. Match is frozen and **amountTrackerId = approvalId = challengeTrackerId**
      1. Everything is always scoped
   3. Uses cross-approval trackers, so **challengeTrackerId / amountTrackerId** must be brute forced respectively.
   4. Else, we need to do one of the following
      1. Make it so trackers are not used
      2. If **amountTrackerId = approvalId = challengeTrackerId**, freeze it w/ approvalId (recommended)
      3. Ekse, freeze it w/ challengeTrackerId
2.
3.
   1. **AND amountTrackerId = approvalId = challengeTrackerId**
      1.
   2. challengeTrackerId / amountTrackerId are brute forced
   3.
      1. If doesn't currently use
   4. Frozen AND &#x20;
   5. Frozen AND ???
      1. The expected behavior of such values can never be affected by break down logic or by other approvals. This must be satisfied for both amounts and challenges.&#x20;
         1. For challenges, this can either be satisfied by the approval not defining/relying on a **merkleChallenge**, or if it does, the **challengeTrackerId** must be completely brute forced in the permissions.
         2. For amounts, this can either be satisfied by 1) the approval not relying on , or if it does, the **amountTrackerId** must be completely brute forced in permissions.
            *
   6.
      * As explained previously, this is the strongly recommended approach for designing approvals in most use cases (that are not using cross-approval logic). If this is satisfied, you can ignore case 2 below. This is because any break-downs cannot use the same tracker ID due to the restriction of them not being able to equal another approval's approval ID.
   7.

Note that if trackers are not used for an approval, it is best practice to keep it scoped to the current approval (**amountTrackerId** = **approvalId** = **challengeTrackerId)** to avoid confusion (avoids Case 2.2.2 since 2.2.1 above is always satisfied).



**Algorithm**&#x20;

Thus, to make a specific tuple non-updatable AND keep the same behavior, you need to:

1. Set the tuple to be non-updatable in the permissions
2. AND make sure that all currently set approvals that overlap with the tuple are non-updatable and keep expected behavior. This should be checked using the new permissions because step 1 may have already handled it for you.
   * If they do not, you have to handle this accordingly, so they do.&#x20;
     * If **amountTrackerId = approvalId = challengeTrackerId**, you can simply brute force the approvalId.
     * Or else, you need to make it satisfy the same expected behavior criteria in another way.&#x20;
       * One way that will always work is you could brute force **amountTrackerId** and **challengeTrackerId**. This will handle the matching approval, but note that it may also affect other approvals. It is up to you whether you want to handle it recursively, or leave it here. If you handle it recursively, note that both tracker IDs need to be satisfied.



**Example**

Let's say you want to forbid ever updating the transferability for badges 1-10, and you have the following approvals currently set.

```json
"collectionApprovals": [
    {
      "fromListId": "Mint",
      "toListId": "All",
      "initiatedByListId": "All",
      "transferTimes": [
        {
          "start": "1691978400000",
          "end": "1723514400000"
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
      "approvalId": "abc",
      "amountTrackerId": "abc",
      "challengeTrackerId": "abc",
      "approvalCriteria": {
        ....
      }
    }
  ]
```



Step 1: Brute force IDs 1-10 in the permissions

<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "fromListId": "All",
  "toListId": "All",
  "initiatedByListId": "All",
  "badgeIds":  [{ "start": "1", "end": "10" }],
  "transferTimes":  [{ "start": "1", "end": "18446744073709551615" }],
  "ownershipTimes":  [{ "start": "1", "end": "18446744073709551615" }],
  "approvalId": "All", //forbids approval "xyz" from being updated
  "amountTrackerId": "All",
  "challengeTrackerId": "All"
  
  
  "permanentlyPermittedTimes": [],
  "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }]
}
</code></pre>

Step 2: Find all matching approvals. In this case, we only have one and it matches because it uses IDs 1-100.

```json
{
    "fromListId": "Mint",
    "toListId": "All",
    "initiatedByListId": "All",
    "transferTimes": [
      {
        "start": "1691978400000",
        "end": "1723514400000"
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
    "approvalId": "abc",
    "amountTrackerId": "abc",
    "challengeTrackerId": "abc",
    "approvalCriteria": {
      ....
    }
  }
```

In this particular case, IDs 1-100 are not brute forced by the permissions yet (11-100 are not). Thus, we need to also make this approval non-updatable. This can be done by adding another permission brute forcing approval "abc".

```json
{
    "fromListId": "All",
    "toListId": "All",
    "initiatedByListId": "All",
    "transferTimes": [
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
    "badgeIds": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "approvalId": "abc",
    "amountTrackerId": "All",
    "challengeTrackerId": "All",
     
     
    "permanentlyPermittedTimes": [],
    "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }]
  }
```

We can now update the permissions, thus making

# 🔐 Permissions

First, read [Permissions](../../overview/how-it-works/manager.md) for an overview.

Note: The [Approved Transfers](transferability-approvals.md) and [Permissions ](../../overview/how-it-works/manager.md)are the most powerful features of the interface, but they can also be the most confusing. For further examples, please reference the [Learn the Interface](../create-and-broadcast-txs/msg-examples.md) section. Please ask for help if needed.

<pre class="language-json"><code class="lang-json"><strong>"collectionPermissions": {
</strong>    "canDeleteCollection": [...],
    "canArchiveCollection": [...],
    "canUpdateOffChainBalancesMetadata": [...],
    "canUpdateStandards": [...],
    "canUpdateCustomData": [...],
    "canUpdateManager": [...],
    "canUpdateCollectionMetadata": [...],
    "canCreateMoreBadges": [...],
    "canUpdateBadgeMetadata": [...],
    "canUpdateCollectionApprovals": [...]
}
</code></pre>

```json
"userPermissions": {
    "canUpdateIncomingApprovals": [...],
    "canUpdateOutgoingApprovals": [...],
    "canUpdateAutoApproveSelfInitiatedOutgoingTransfers": [...],
    "canUpdateAutoApproveSelfInitiatedIncomingTransfers": [...],
}
```

The **canUpdateIncomingApprovals** and **canUpdateOutgoingApprovals** follow the same interface as **canUpdateCollectionApprovals** minus automatically populating the user's address for to / from for incoming / outgoing, respectively. We only explain the collection approval permission to avoid repetition.

```typescript
export interface ActionPermission<T extends NumberType> {
  permanentlyPermittedTimes: UintRange<T>[];
  permanentlyForbiddenTimes: UintRange<T>[];
}
```

```typescript
export interface TimedUpdatePermission<T extends NumberType> {
  timelineTimes: UintRange<T>[];
  
  permanentlyPermittedTimes: UintRange<T>[];
  permanentlyForbiddenTimes: UintRange<T>[];
}
```

```typescript
export interface TimedUpdateWithBadgeIdsPermission<T extends NumberType> {
  timelineTimes: UintRange<T>[];
  badgeIds: UintRange<T>[];
  
  permanentlyPermittedTimes: UintRange<T>[];
  permanentlyForbiddenTimes: UintRange<T>[];
}
```

```typescript
export interface BalancesActionPermission<T extends NumberType> {
  badgeIds: UintRange<T>[];
  ownershipTimes: UintRange<T>[];
  
  permanentlyPermittedTimes: UintRange<T>[];
  permanentlyForbiddenTimes: UintRange<T>[];
}
```

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

### Manager

The collectionPermissions only apply to the current manager of the collection. In other words, the manager is the only one who is able to execute permissions. If there is no manager for a collection, no permissions can be executed.

The current manager is determined by the **managerTimeline.** Transferring the manager is facilitated via the **canUpdateManager** permission.

```json
"managerTimeline": [
  {
    "manager": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
    "timelineTimes": [
      {
        "start": 1,
        "end": "18446744073709551615"
      }
    ]
  }
]
```

### **Permitted and Forbidden Times**

Permissions allow you to define permitted or forbidden times to be able to execute a permission.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**States**

There are three states that a permission can be in at any one time:

1. **Forbidden + Permanently Frozen (permanentlyForbiddenTimes):** This permission is forbidden and will always remain forbidden.
   1. If a permission is explicitly allowed via the **permanentlyPermittedTimes, it will ALWAYS be allowed** during those permanentlyPermittedTimes (can't change it).
2. **Permitted + Not Frozen (Unhandled):** This permission is currently permitted but can be changed to one of the other two states.
   1. If not explicitly permitted or forbidden - NEUTRAL (not defined or unhandled), **permissions are ALLOWED by default** but can later be set to be permanently allowed or disallowed. There is no "forbidden currently but updatable" state.
3. **Permitted + Permanently Frozen (permanentlyPermittedTimes):** This permission is forbidden and will always remain permitted
   1. If a permission is explicitly forbidden via the **permanentlyForbiddenTimes, it will ALWAYS be disallowed** during those permanentlyForbiddenTimes.

There is no forbidden + not frozen state because theoretically, it could be updated to permitted at any time and executed (thus making it permitted).

**Examples**

This means the permission is permanently forbidden and frozen.

```typescriptreact
permanentlyPermittedTimes: []
permanentlyForbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
```

This means it is permanently allowed and frozen.

```typescriptreact
permanentlyForbiddenTimes: []
permanentlyPermittedTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
```

This means it is allowed currently but neutral and can be changed to be always permitted or always forbidden in the future.

```
permanentlyForbiddenTimes: []
permanentlyPermittedTimes: []
```

### First Match Policy

All permissions are a linear array where each element may have some criteria as well as **permanentlyForbiddenTimes** or **permanentlyPermittedTimes.** It can be interpreted as if the criteria matches, the permission is permitted or forbidden according to the defined times, respectively.

We do not allow times to be in both the permanentlyPermittedTimes and permanentlyForbiddenTimes array simultaneously.

**Unlike approvals, we only allow taking the first match in the case criteria satisfies multiple elements in the permissions array.** All subsequent matches are ignored. This makes it so that for any time and for each criteria combination, there is a deterministic permission state (permitted, forbidden, or neutral). This means you have to carefully design your permissions because order and overlaps matter.

Ex: If we have the following permission definitions in an array \[elem1, elem2]:

1. ```
   timelineTimes: [{ start: 1, end: 10 }]

   permanentlyPermittedTimes: []
   permanentlyForbiddenTimes: [{ start: 1, end: 10 }]
   ```
2. ```
   timelineTimes: [{ start: 1, end: 100 }]

   permanentlyForbiddenTimes: []
   permanentlyPermittedTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
   ```

In this case, the timeline times 1-10 will be forbidden ONLY from times 1-10 because we take the first element that matches for that specific criteria (which is permanentlyPermittedTimes: \[], permanentlyForbiddenTimes: \[1 to 10]).

Times 11-100 would be permanently permitted since the first match for those times is the second element.

Similar to approved transfers, even though we allow range logic to be specified, we first expand everything maintaining order to their singular values (one value, no ranges) before checking for our first match.

### Satisfying Criteria

All criteria must be satisfied for it to be a match. For example, for the **canCreateMoreBadges** permission, the criteria involves which badge IDs can the manager create more of and at what times can they be owned (circulating times).

```
badgeIds: [{ start: 1, end: 10 }]
ownershipTimes: [{ start 1, end: 10 }]

permanentlyForbiddenTimes: []
permanentlyPermittedTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
```

This would result in the manager being able to create more of badges IDs 1-10 which can be owned from times 1-10.

However, this permission **does not** specify whether they can create more of badge ID 1 at time 11 or badge ID 11 at time 1. These combinations are considered unhandled or not defined by the permission definition above.

**Common Misunderstanding**

A common misunderstanding is that if the permission below is appended after the above one, this would forbid badges 11+ from ever being created. However, creating badge IDs 11+ at times 11+ would still be unhandled and allowed by default.

```
badgeIds: [{ start: 11, end: Max }]
ownershipTimes: [{ start 1, end: 10 }]

permanentlyForbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
permanentlyPermittedTimes: []
```

To permanently forbid all badgeIds, you must brute force ALL other combinations such as

```
badgeIds: [{ start: 11, end: Max }]
ownershipTimes: [{ start: 1, end: Max }] // 1-10 never gets matched to bc of first match
//can also do start: 11

permanentlyForbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
permanentlyPermittedTimes: []
```

**Approved Transfers Criteria**

For approval permissions like below, this would mean that updating any approval from the "Mint" address is permanently forbidden. However, it does not apply to approvals from any other address, even if all other N-1 criteria is satisfied.

Also, if we add another permission definition after this one in the array with **fromListId** = "Mint", this would never be matched to because the first one brute forces all possible combinations, so first match always matches to this one.

```json
"canUpdateCollectionApprovals": [
  {
    "fromListId": "Mint",
    "toListId": "AllWithMint",
    "initiatedByListId": "AllWithMint",
    "timelineTimes": [
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
    "approvalId": "All",
    "approvalTrackerId": "All",
    "challengeTrackerId": "All",
    "permanentlyPermittedTimes": [],
    "permanentlyForbiddenTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ]
  }
]
```

#### **Importance of Handling All Values**

Let's say we have the following **where** we do not handle badge ID 1.

```
"canUpdateCollectionApprovals": [
  {
    "fromListId": "AllWithMint",
    "toListId": "AllWithMint",
    "initiatedByListId": "AllWithMint",
    "timelineTimes": [
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
    "badgeIds": [
      {
        "start": "2",
        "end": "18446744073709551615"
      }
    ],
    "ownershipTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "approvalId": "All",
    "approvalTrackerId": "All",
    "challengeTrackerId": "All",
    "permanentlyPermittedTimes": [],
    "permanentlyForbiddenTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ]
  }
]
```

Even though all other combinations are unhandled, we can create / update any approval of badge ID 1 because it is unhandled and allowed (neutral) by default.

### **Permission Categories**

There are five categories of permissions, each with different criteria that must be matched with. If you get confused with the different time types, refer to [Different Time Types](different-time-fields.md) for examples and explanations.

```json
"canDeleteCollection": [], //ActionPermission[]
"canArchiveCollection": [], //TimedUpdatePermission[]
"canUpdateContractAddress": [], //TimedUpdatePermission[]
"canUpdateOffChainBalancesMetadata": [], //TimedUpdatePermission[]
"canUpdateStandards": [], //TimedUpdatePermission[]
"canUpdateCustomData": [], //TimedUpdatePermission[]
"canUpdateManager": [], //TimedUpdatePermission[]
"canUpdateCollectionMetadata": [], //TimedUpdatePermission[]
"canCreateMoreBadges": [], //BalancesActionPermission[]
"canUpdateBadgeMetadata": [], //TimedUpdateWithBadgeIdsPermission[]
"canUpdateCollectionApprovals": [] //ApprovedTransferPermission[]
```

```json
"canUpdateIncomingApprovals": [], //ApprovedTransferPermission[] (w/o to fields)
"canUpdateOutgoingApprovals": [], //ApprovedTransferPermission[] (w/o from fields)
"canUpdateAutoApproveSelfInitiatedOutgoingTransfers": [], //ActionPermission[]
"canUpdateAutoApproveSelfInitiatedIncomingTransfers": [], //ActionPermission[]
```

* **ActionPermission**: Simplest (no criteria). Just denotes what times the action is executable or not.
  * <pre class="language-typescript"><code class="lang-typescript"><strong>{
    </strong>  permanentlyPermittedTimes: UintRange&#x3C;T>[];
      permanentlyForbiddenTimes: UintRange&#x3C;T>[];
    }
    </code></pre>
* **TimedUpdatePermission**: For what timelineTimes, can the manager update the scheduled value?
  * timelineTimes corresponds to the scheduled times of a timeline-based value such as badge metadata or collection metadata. The permanentlyPermittedTimes and permanentlyForbiddenTimes correspond to when the manager can update such values.
  * ```typescript
    {
      timelineTimes: UintRange<T>[];
      
      permanentlyPermittedTimes: UintRange<T>[];
      permanentlyForbiddenTimes: UintRange<T>[];
    }
    ```
* **TimedUpdateWithBadgeIdsPermission**: For what timelineTimes / badge ID combinations can I update the scheduled value?
  * ```typescript
    {
      timelineTimes: UintRange<T>[];
      badgeIds: UintRange<T>[];
      
      permanentlyPermittedTimes: UintRange<T>[];
      permanentlyForbiddenTimes: UintRange<T>[];
    }
    ```
* **BalancesActionPermission**: For what badge ID / ownershipTimes combinations can the manager execute the permission?
  * ```typescript
    {
      badgeIds: UintRange<T>[];
      ownershipTimes: UintRange<T>[];
      
      permanentlyPermittedTimes: UintRange<T>[];
      permanentlyForbiddenTimes: UintRange<T>[];
    }
    ```
* **ApprovalPermission**: For what timeline times AND what transfer combinations (see [Representing Transfers](transferability-approvals.md)), can I update the approval criteria? See section below for further details.
  * **IDs:** The IDs are used for locking specific approvals or trackers. This is because sometimes it may not be sufficient to just lock a specific (from, to, initiator, time, badge IDs, ownershipTimes) combination due to multiple approvals matching the same criteria.
  * **ID Shorthand Options:** To specify IDs, you can use the "All" reserved ID to represent all IDs, or you can use other shorthand methods such as "!xyz" to denote all IDs but xyz. These shorthands are the same as reserved lists, so we refer you[ there for more info](address-lists-lists.md). Just replace the addresses with the IDs.
  * Ex: I cannot update the approvals for the combinations ("All", "All", "All", 1-100, 1-10, 1-10, "All",  "All",  "All") tuple.
    * <pre class="language-typescript"><code class="lang-typescript"><strong>{
      </strong>  fromListId: string;
        toListId: string;
        initiatedByListId: string;
        transferTimes: UintRange&#x3C;T>[];
        badgeIds: UintRange&#x3C;T>[];
        ownershipTimes: UintRange&#x3C;T>[];
        approvalId: string
        amountTrackerId: string
        challengeTrackerId: string
        
        
        permanentlyPermittedTimes: UintRange&#x3C;T>[];
        permanentlyForbiddenTimes: UintRange&#x3C;T>[];
      }
      </code></pre>

### **Approval Permissions**

**Break Down Logic**

Because of the way breakdown logic is performed, the following is allowed.

Permission: Badge IDs 2-10 are locked

Before: 1-10 -> Criteria ABC

After: 1 -> Criteria XYZ, 2-10 -> Criteria ABC

**Freezing Specific Values**

With the way trackers work, it is important to handle approval permissions correctly to protect against cross-approval logic or break-down attacks. Let's start by establishing some definitions

* Non-Updatable: Values are considered non-updatable according to the permissions if they brute force ALL possible combinations for that values. For example, the following permission in the code block brute forces badge IDs 2-10. In this case, approvals with IDs 2-10 can never be created, deleted, or updated again, but this may not handle expected behavior changes because of how trackers work. Note that this can also apply to specific value pairs (e.g. IDs 1-10 and Times  Mon-Fri). You will just need to handle overlaps with multiple values, rather than one.&#x20;
  * <pre class="language-json"><code class="lang-json"><strong>{
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
* Expected Behavior: Expected behavior not only encompasses non-updatability, but it also makes sure that nothing any other approval or update can do can affect the expected behavior of this approval. If you are implementing cross-approval logic, this step is especially important.
  * For example, lets say we want to freeze IDs 501-1000 and have an incrementing mint of x1 of ID 1, x1 of ID 2, up to ID 1000. If we simply freeze the IDs 501-1000, the approval could still be deleted for IDs 1-500, and the increment number (tracker) will then never reach 501 because it will be out of bounds every time. Thus, expected behavior changes.
  * Or, let's say you create an approval for badges 1-100 with **challengeTrackerId** = "abc" and **approvalId** = "xyz", and you lock updating any approval for badges 1-100 but don't specify anything about the **challengeTrackerId**. While you cannot create/edit a new approval for badges 1-100, you can create a new approval for badge IDs 101+ with the same **challengeTrackerId** = "abc". In storage, the trackers are inherently linked, so when leaf #5 is used up in the new 101+ approval, leaf #5 will also be marked as used in the 1-100 approval. This totally messes up how the first approval operates.



Non-Updatable and Same Expected Behavior Algorithm

* A subset of a defined approval (**from, to, initiatedByList, badgeIds, transferTimes, ownershipTimes, approvalId, challengeTrackerId, amountTrackerId**) is non-updatable AND will always keep its expected behavior if 1 and 2 are satisfied below
  1. One of its values is COMPLETELY non-updatable (for an approval subset w/ badge IDs 2-10, the permission below would satisfy this, but if it was for 2-11, it would not). This can be satisfied for any property&#x20;
  2. AND, one of the following is satisfied:
     1. **amountTrackerId = approvalId = challengeTrackerId**&#x20;
        * As explained previously, this is the strongly recommended approach for most use cases (that are not using cross-approval logic). If this is satisfied, you don't ever have to deal with the cases below. This is because any break-downs cannot use the same tracker ID due to the restriction of them not being able to equal another approval's approval ID.
     2. The expected behavior of such values can never be affected by break down logic or by other approvals. This must be satisfied for both amounts and challenges.&#x20;
        1. For challenges, this can either be satisfied by the approval not defining/using a **merkleChallenge**, or if it does, the **challengeTrackerId** must be completely non-updatable in permissions.
        2. For amounts, this can either be satisfied by 1) the approval not relying on **approvalAmounts, predeterminedBalances**, or **maxNumTransfers**, or if it does, the **amountTrackerId** must be completely non-updatable in permissions.
           * There is an edge case where the approval relies on **predeterminedBalances** but only for challenges (**orderCalculationMethod**.**useMerkleChallengeLeafIndex**). This is fine as long as it doesn't rely on the other two.
* To see if an approval itself is non-updatable AND keeps the same behavior, apply the above to the whole approval, not just a subset
* Note that if trackers are not used for an approval, it is best practice to keep it scoped to the current approval (**amountTrackerId** = **approvalId** = **challengeTrackerId)** to avoid confusion



Thus, to make a specific subset non-updatable AND keep the same behavior, you need to:

1. Set the values themselves to be non-updatable by brute forcing them in the permissions
2. AND make sure that all approvals that overlap with the subset maintain the expected behavior for that subset. This should be checked using the new permissions because step 1 may have already handled it for you.
   * If they do not, you have to handle this accordingly, so they do.&#x20;
     * If **amountTrackerId = approvalId = challengeTrackerId**, you can simply make the **approvalId** non-updatable by adding a new permission, such as "xyz" below. Note you can also do multiple in one with shorthands such as "xyz:abc:123".
       * ```json
         {
           "fromListId": "AllWithMint",
           "toListId": "AllWithMint",
           "initiatedByListId": "AllWithMint",
           "badgeIds":  [{ "start": "1", "end": "18446744073709551615" }],
           "transferTimes":  [{ "start": "1", "end": "18446744073709551615" }],
           "ownershipTimes":  [{ "start": "1", "end": "18446744073709551615" }],
           "approvalId": "xyz", //forbids approval "xyz" from being updated
           "amountTrackerId": "All",
           "challengeTrackerId": "All",
           "permanentlyPermittedTimes": [],
           "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }]
         }
         ```
     * Or else, you need to make it satisfy the same expected behavior criteria in another way.&#x20;
       * One way that will always work is you could make the **amountTrackerId** and **challengeTrackerId** non-updatable. This will handle the matching approval, but note that it may also affect other approvals. It is up to you whether you want to handle it recursively, or leave it here. If you handle it recursively, note that both tracker IDs need to be satisfied.

### **User Permissions**

Besides the collection permissions, there are also userPermissions that can be set.

```json
"userPermissions": {
    "canUpdateIncomingApprovals": [],
    "canUpdateOutgoingApprovals": [],
    "canUpdateAutoApproveSelfInitiatedOutgoingTransfers": [],
    "canUpdateAutoApproveSelfInitiatedIncomingTransfers": [],
}
```

Typically, these will remain empty, so that the user can always have full control over their approvals. If empty, they are permitted by default (but not frozen).&#x20;

However, setting user permissions can be leveraged in some cases for specific purposes.

* Locking that a specific badge can never be transferred out of the account
* Locking that a specific approval is always set and uneditable so that two mutually distrusting parties can use the address as an escrow

**Defaults**

We give the option for the collection to define default permissions. These will be used as the starting values when the balance is initially created in storage. This can be used in tandem with the other defaults. The default permissions are also not typically used, but again can be used in certain situations. For example,

* By default, approve all incoming transfers and lock the permission so all transfers always have incoming approvals and can never be disapproved.

### **Permissions vs Actual Values**

Note that the update permissions (**ApprovalPermissions, TimedUpdatePermission,** and **TimedUpdateWithBadgeIdsPermission)** correspond to the UPDATABILITY of the values and are separate from the actual values themselves. The permission does not correlate to what the currently set values are.

Similarly with the **BalancesActionPermission**, it has no bearing on what the currently minted supplys are. It refers to the EXECUTABILITY of creating new badges.

For example, if **canCreateMoreBadges** is set to this

<pre><code><strong>badgeIds: [{ start: 1, end: 10 }]
</strong>ownershipTimes: [{ start 1, end: 10 }]

permanentlyForbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
permanentlyPermittedTimes: []
</code></pre>

it has no bearing on what the current circulating supplys are, but it says that whatever they are currently, no more of IDs 1-10 for times 1-10 can be created.

### **Examples**

See [Example Msgs](broken-reference/) for further examples.

```json
"collectionPermissions": {
    "canArchiveCollection": [],
    "canCreateMoreBadges": [
      {
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
        "permanentlyPermittedTimes": [],
        "permanentlyForbiddenTimes": [
          {
            "start": "1",
            "end": "18446744073709551615"
          }
        ],
      }
    ],
    "canDeleteCollection": [],
    "canUpdateBadgeMetadata": [],
    "canUpdateCollectionApprovals": [
      {
        "fromListId": "AllWithMint",
        "toListId": "AllWithMint",
        "initiatedByListId": "AllWithMint",
        "timelineTimes": [
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
        "approvalTrackerId": "All",
        "challengeTrackerId": "All",
        "permanentlyPermittedTimes": [],
        "permanentlyForbiddenTimes": [
          {
            "start": "1",
            "end": "18446744073709551615"
          }
        ]
      }
    ],
    "canUpdateCollectionMetadata": [],
    "canUpdateContractAddress": [],
    "canUpdateCustomData": [],
    "canUpdateManager": [],
    "canUpdateOffChainBalancesMetadata": [],
    "canUpdateStandards": []
  },
```

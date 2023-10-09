# 🔐 Permissions

First, read [Permissions](../../overview/concepts/manager.md) for an overview.

Note: The [Approved Transfers](approvals.md) and [Permissions ](../../overview/concepts/manager.md)are the most powerful features of the interface, but they can also be the most confusing. For further examples, please reference the [Learn the Interface](../interface-examples.md) section. Please ask for help if needed.

```json
"collectionPermissions": {
    "canDeleteCollection": [],
    "canArchiveCollection": [],
    "canUpdateContractAddress": [],
    "canUpdateOffChainBalancesMetadata": [],
    "canUpdateStandards": [],
    "canUpdateCustomData": [],
    "canUpdateManager": [],
    "canUpdateCollectionMetadata": [],
    "canCreateMoreBadges": [],
    "canUpdateBadgeMetadata": [],
    "canUpdateCollectionApprovals": []
}
```

```json
"userPermissions": {
    "canUpdateIncomingApprovals": [],
    "canUpdateOutgoingApprovals": []
}
```

Note that the **canUpdateIncomingApprovals** and **canUpdateOutgoingApprovals** follow the same interface as **canUpdateCollectionApprovals** minus automatically populating the user's address for to / from for incoming / outgoing, respectively.

### **Permitted and Forbidden Times**

Permissions allow you to define permitted or forbidden times to be able to execute a permission.

All permissions are a linear array of (criteria -> permitted/forbiddenTimes) maps. If the criteria matches, the permission is permitted or forbidden at a specific time dependent on the defined permitted/forbiddenTimes.

1\) If a permission is explicitly allowed via the **permittedTimes, it will ALWAYS be allowed** during those permittedTimes (can't change it).

2\) If a permission is explicitly forbidden via the **forbiddenTimes, it will ALWAYS be disallowed** during those forbiddenTimes.

3\) If not explicitly permitted or forbidden - NEUTRAL (not defined), **permissions are ALLOWED by default (unhandled)** but can later be set to be explicitly allowed or disallowed. There is no "forbidden currently but updatable" state.&#x20;

4\) We do not allow times to be in both the permittedTimes and forbiddenTimes array simultaneously.



So for example, this means the permission is permanently forbidden and frozen.

```typescriptreact
permittedTimes: []
forbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
```

This means it is permanently allowed and frozen.

```typescriptreact
forbiddenTimes: []
permittedTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
```

This means it is allowed currently but neutral and can be changed to be always permitted or always forbidden in the future.&#x20;

```
forbiddenTimes: []
permittedTimes: []
```

### First Match Policy

Unlike approvals, we only allow take the first match, in the case criteria satisfies multiple elements in the permissions array. All subsequent matches are ignored. **This makes it so that at any time, there is only ONE deterministic permitted/forbiddenTimes for a given set of criteria.** This means you have to carefully design your permissions because order and overlaps matter.

Ex: If we have the following permission definitions in an array:&#x20;

1. ```
   timelineTimes: [{ start: 1, end: 10 }]

   permittedTimes: []
   forbiddenTimes: [{ start: 1, end: 10 }]
   ```
2. ```
   timelineTimes: [{ start: 1, end: 100 }]

   forbiddenTimes: []
   permittedTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
   ```

In this case, the timeline times 1-10 will be forbidden ONLY from times 1-10 because we only take the first element that matches for that specific criteria (which is permittedTimes: \[], forbiddenTimes: \[1 to 10]).&#x20;

Times 11-100 would be permanently permitted since the first match for those times is the second element.

Similar to approved transfers, even though we allow range logic to be specified, we first expand everything maintaining order to their singular values (one value, no ranges) before checking for our first match.

### Satisfying Criteria

All criteria must be satisfied for it to be a match. For example, for the **canCreateMoreBadges** permission, the criteria involves which badge IDs can the manager create more of and at what times can they be owned (circulating times).

```
badgeIds: [{ start: 1, end: 10 }]
ownershipTimes: [{ start 1, end: 10 }]

forbiddenTimes: []
permittedTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
```

This would result in the manager being able to create more of badges IDs 1-10 which can be owned from times 1-10.&#x20;

However, this permission **does not** specify whether they can create more of badge ID 1 at time 11 or badge ID 11 at time 1. These combinations are considered **unhandled** or not defined by the permission definition above.&#x20;

**Common Misunderstanding**

A common misunderstanding is that if the permission below is appended after the above one, this would forbid badges 11+ from ever being created. However, creating badge IDs 11+ at times 11+ would still be unhandled and allowed by default.

```
badgeIds: [{ start: 11, end: Max }]
ownershipTimes: [{ start 1, end: 10 }]

forbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
permittedTimes: []
```

To permanently forbid all badgeIds, you must brute force ALL other combinations such as&#x20;

```
badgeIds: [{ start: 11, end: Max }]
ownershipTimes: [{ start: 1, end: Max }] // 1-10 never gets matched to bc of first match
//can also do start: 11

forbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
permittedTimes: []
```

**Approved Transfers Criteria**

For approved transfer permissions like below, this would mean that updating any approval from the "Mint" address is permanently forbidden. However, it does not apply to approvals from any other address, even if all other N -1 criteria is satisfied.&#x20;

Also, if we add another permission definition after this one with **fromMappingId** = "Mint", this would never be matched to because the first one brute forces all possible combinations, so first match always matches to this one.

```json
"canUpdateCollectionApprovals": [
  {
    "fromMappingId": "Mint",
    "toMappingId": "AllWithMint",
    "initiatedByMappingId": "AllWithMint",
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
    "permittedTimes": [],
    "forbiddenTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ]
  }
]
```

### Shorthand Options

Note that we also reserve the "!" prefix for inverting a given mapping ID and also reserve specific mapping IDs (see [Address Mappings](address-mappings-lists.md)).

The same shorthand options can be applied to the trackerId ("!id123" means all IDs except "id123"). Use "All" to represent all tracker IDs.

### **Importance of Handling All Values**

Let's say we have the following where we do not handle badge ID 1.

```
"canUpdateCollectionApprovals": [
  {
    "fromMappingId": "AllWithMint",
    "toMappingId": "AllWithMint",
    "initiatedByMappingId": "AllWithMint",
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
    "approvalTrackerId": "All",
    "challengeTrackerId": "All",
    "permittedTimes": [],
    "forbiddenTimes": [
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
"canDeleteCollection": [], //ActionPermission
"canArchiveCollection": [], //TimedUpdatePermission
"canUpdateContractAddress": [], //TimedUpdatePermission
"canUpdateOffChainBalancesMetadata": [], //TimedUpdatePermission
"canUpdateStandards": [], //TimedUpdatePermission
"canUpdateCustomData": [], //TimedUpdatePermission
"canUpdateManager": [], //TimedUpdatePermission
"canUpdateCollectionMetadata": [], //TimedUpdatePermission
"canCreateMoreBadges": [], //BalancesActionPermission
"canUpdateBadgeMetadata": [], //TimedUpdateWithBadgeIdsPermission
"canUpdateCollectionApprovals": [] //ApprovedTransferPermission
```

* **ActionPermission**: Simplest (no criteria). Just denotes what times the action is executable or not.&#x20;
  * ```typescript
    {
      permittedTimes: UintRange<T>[];
      forbiddenTimes: UintRange<T>[];
    }
    ```
* **TimedUpdatePermission**: For what timelineTimes, can the manager update the scheduled value?
  * Note timelineTimes corresponds to the scheduled times of a timeline-based value such as badge metadata or collection metadata. The permittedTimes and forbiddenTimes correspond to when the manager can update such values.
  * ```typescript
    {
      timelineTimes: UintRange<T>[];
      
      permittedTimes: UintRange<T>[];
      forbiddenTimes: UintRange<T>[];
    }
    ```
* **TimedUpdateWithBadgeIdsPermission**: For what timelineTimes AND what badge IDs can I update the scheduled value?
  * ```typescript
    {
      timelineTimes: UintRange<T>[];
      badgeIds: UintRange<T>[];
      
      permittedTimes: UintRange<T>[];
      forbiddenTimes: UintRange<T>[];
    }
    ```
* **BalancesActionPermission**: For what badge IDs and ownershipTimes can the manager execute the permission?
  * ```typescript
    {
      badgeIds: UintRange<T>[];
      ownershipTimes: UintRange<T>[];
      
      permittedTimes: UintRange<T>[];
      forbiddenTimes: UintRange<T>[];
    }
    ```
* **UpdateApprovedTransferPermission**: For what timeline times AND what transfer combinations (see [Representing Transfers](approvals.md)), can I update the approval criteria? See section below for further details.
  * Ex: I cannot update the approvals for the combinations ("All", "All", "All", 1-100, 1-10, 1-10, "All", "All") tuple, thus making the transferability locked for those transfer combinations.
    * <pre class="language-typescript"><code class="lang-typescript"><strong>{
      </strong>  fromMappingId: string;
        toMappingId: string;
        initiatedByMappingId: string;
        transferTimes: UintRange&#x3C;T>[];
        badgeIds: UintRange&#x3C;T>[];
        ownershipTimes: UintRange&#x3C;T>[];
        approvalTrackerId: string
        challengeTrackerId: string
        
        permittedTimes: UintRange&#x3C;T>[];
        forbiddenTimes: UintRange&#x3C;T>[];
      }
      </code></pre>

### **ApprovedTransferPermissions**

**What do we check if updating an approval is locked?**

After expanding to a singular value tuple (bob, alice, bob, 1, 1, ...), we fetch ALL matches and check the following to ensure that nothing has changed:

1\) If previously mapped to a disapproval, it must also map to a disapproval now.

2\) For all matches, ensure that all **approvalCriteria** remains unchanged. So if we had 3 prior matches, we check that all 3 are exactly the same. If there are now 2 matches, this isn't allowed, or if one of the three's criteria changes, this is not allowed.

**How are break downs handled?**

Like mentioned above, we handle everything broken down into a singular value tuple. So for example, even though you have locked badge IDs 2-10 from being updated, the following would be allowed because of the break down logic.

Before: 1-10 -> APPROVED, Criteria ABC

After: 1 -> APPROVED, Criteria XYZ, 2-10 -> Criteria ABC

**Tracker IDs**

An important part in setting an approval permission is ensuring that **approvalTrackerId** and **challengeTrackerId** are handled correctly.

Let's explain via a scenario. Let's say you create an approval that tracks used Merkle challenge leaves with a one use per leaf limit. By default, due to the way **challengeTrackerId** works, multiple approvals can have the same **challengeTrackerId.**&#x20;

Let's say you create an approval for badges 1-100 with **challengeTrackerId** = "abc", and you lock updating any approval for badges 1-100 but don't specify anything about the **challengeTrackerId**.

While you cannot create a new approval for badges 1-100, you can create a new approval for badge IDs 101+ with the same **challengeTrackerId** = "abc". In storage, the challenges are inherently linked, so when leaf #5 is used up in the new 101+ approval, leaf #5 will also be marked as used in the 1-100 approval. This totally messes up how the first approval operates.

So, note that to ensure a **challengeTrackerId** and/or **approvalTrackerId** is never updated / used again, you must brute force ALL combinations with that specific ID.

```json
{
  "fromMappingId": "AllWithMint",
  "toMappingId": "AllWithMint",
  "badgeIds" [{ 1-Max }],
  ...
  "challengeTrackerId": "xyz",
  "permittedTimes": [],
  "forbiddenTimes": [
    {
      "start": "1",
      "end": "18446744073709551615"
    }
  ]
}
```

You can also use the "All" reserved ID to represent all tracker IDs cannot be updated, for example, or use the manipulation options to represent all, invert, etc.

### **User Permissions**

Besides the collection permissions, there are also userPermissions that can be set.&#x20;

```json
"userPermissions": {
    "canUpdateIncomingApprovals": [],
    "canUpdateOutgoingApprovals": []
}
```

Typically, these will remain empty, so that the user can always update their approvals. However, they can be leveraged in some cases for specific purposes.

* Locking that a specific badge can never be transferred out of the account
* Locking that a specific approval is always set and uneditable so that two mutually distrusting parties can use the address as an escrow

**Defaults**

We give the option for the collection to define **defaultUserPermissions.** These will be used as the starting values when the balance is initially created in storage. This can be used in tandem with **defaultOutgoingApprovals** and **defaultIncomingApprovals.**

The **defaultUserPermissions** are also not typically used, but can be used in certain situations. For example,

* By default, approve all incoming transfers and lock the permission so all transfers always have incoming approvals and can never be disapproved.

### **Permissions vs Actual Values**

Note that the update permissions (**UpdateApprovedTransferPermission, TimedUpdatePermission,** and **TimedUpdateWithBadgeIdsPermission)** correspond to the UPDATABILITY of the values and are separate from the actual values themselves. The permission does not correlate to what the currently set values are.

Similarly with the **BalancesActionPermission**, it has no bearing on what the currently minted supplys are. It refers to the EXECUTABILITY of creating new badges.



For example, if **canCreateMoreBadges** is set to this

<pre><code><strong>badgeIds: [{ start: 1, end: 10 }]
</strong>ownershipTimes: [{ start 1, end: 10 }]

forbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
permittedTimes: []
</code></pre>

it has no bearing on what the current circulating supplys are, but it says that whatever they are currently,  no more of IDs 1-10 for times 1-10 can be created.

### **Examples**

See [Example Msgs](broken-reference) for further examples.

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
        "permittedTimes": [],
        "forbiddenTimes": [
          {
            "start": "1",
            "end": "18446744073709551615"
          }
        ],
        "badgeIdsOptions": {
          "invertDefault": false,
          "allValues": false,
          "noValues": false
        },
        "ownershipTimesOptions": {
          "invertDefault": false,
          "allValues": false,
          "noValues": false
        },
        "permittedTimesOptions": {
          "invertDefault": false,
          "allValues": false,
          "noValues": false
        },
        "forbiddenTimesOptions": {
          "invertDefault": false,
          "allValues": false,
          "noValues": false
        }
      }
    ],
    "canDeleteCollection": [],
    "canUpdateBadgeMetadata": [],
    "canUpdateCollectionApprovals": [
      {
        "fromMappingId": "AllWithMint",
        "toMappingId": "AllWithMint",
        "initiatedByMappingId": "AllWithMint",
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
        "permittedTimes": [],
        "forbiddenTimes": [
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

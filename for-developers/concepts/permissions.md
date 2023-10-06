# 🔐 Permissions

First, read [Permissions](../../overview/concepts/manager.md) for an overview.

Note: The [Approved Transfers](approvals.md) and [Permissions ](../../overview/concepts/manager.md)are the most powerful features of the interface, but they can also be the most confusing. For further examples, please reference the [Learn the Interface](../learn-the-interface/) section. Please ask for help if needed.



### **Permitted and Forbidden Times**

Permissions allow you to define permitted or forbidden times to be able to execute a permission.

All permissions are a linear array of (criteria -> permitted/forbiddenTimes) maps. If the criteria matches, the permission is permitted or forbidden at a specific time dependent on the defined permitted/forbiddenTimes.

1\) If a permission is explicitly allowed via the permittedTimes, it will ALWAYS be allowed during those permittedTimes (can't change it).

2\) If a permission is explicitly forbidden via the forbidden times, it will ALWAYS be disallowed during those forbiddenTimes.

3\) If not explicitly permitted or forbidden - NEUTRAL (not defined), permissions are ALLOWED by default but can later be set to be explicitly allowed or disallowed.

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

This means it is allowed currently but can be changed in the future.&#x20;

```
forbiddenTimes: []
permittedTimes: []
```

### First Match Policy

Unlike approvals, we only allow take the first match, in the case criteria satisfies multiple elements in the permissions array. All subsequent matches are ignored. This makes it so that at any time, there is only ONE deterministic match for a given set of criteria.&#x20;

Ex: If we have the following permission definitions:&#x20;

1. ```
   timelineTimes: [{ start: 1, end: 10 }]

   permittedTimes: []
   forbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
   ```
2. ```
   timelineTimes: [{ start: 1, end: 100 }]

   forbiddenTimes: []
   permittedTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
   ```

In this case, the timeline times 1-10 will be forbidden because we only take the first match for that specific criteria. 11-100 would be approved.

Similar to approved transfers, even though we allow range logic to be specified, we first expand everything maintaining order to their singular values (one value, no ranges) before checking for matches.

### Satisfying Criteria

All criteria must be satisfied  for it to be a match. For example, for the can create more badges permission, the criteria involves which badge IDs can the manager create more of and at what ownership times.

```
badgeIds: [{ start: 1, end: 10 }]
ownershipTimes: [{ start 1, end: 10 }]

forbiddenTimes: []
permittedTimes: [{ start: 1, end: GO_MAX_UINT_64 }]
```

This would result in the manager being able to create more of badges IDs 1-10 which can be owned from times 1-10.&#x20;

However, this permission **does not** specify whether they can create more of badge ID 1 at time 11 or badge ID 11 at time 1. These combinations are considered unhandled or not defined by the permission definition above.

### **Combinations / Default Values**

Similar to the approved transfers, we allow you to define default values and an array of combinations that manipulate (invert, all, none, or keep) the default values to create different combinations of permitted/forbiddenTimes. It is used for shorthand and avoiding repeating values.

```typescript
{
  defaultValues: BalancesActionPermissionDefaultValues<T>;
  combinations: BalancesActionPermissionCombination[];
}
```

We apply the following pseudocode

```
allPermissionsArr = []
for permission of permissions
    for combination of permission.combinations {
        currPermission = manipulate default values according to combination options
        allPermissionsArr.push(currPermission)
    }
}
```

Couple notes:

\-With a first match policy, order matters. So, note that combination 100 of element 1 comes before the first combination of element 2.

\-Since we expand the combinations array, there must be 1 or more elements to be meaningful. Empty arrays are no ops. If you do not use any options, you still need to define an empty combination such as:

```
[
    {}
]
```

### **Permission Categories**

There are five categories of permissions, each with different criteria that must be matched with. If you get confused with the different time types, refer to [Different Time Types](different-time-fields.md) for examples and explanations.

* **ActionPermission**: Simplest (no criteria). Just denotes what times the action is executable or not.
  * ```typescript
    {
      permittedTimes: UintRange<T>[];
      forbiddenTimes: UintRange<T>[];
    }
    ```
* **TimedUpdatePermission**: For what timelineTimes, can the manager update a timeline-based value?
  * Note timelineTimes corresponds to the times of a timeline-based value such as badge metadata or collection metadata. The permittedTimes and forbiddenTimes correspond to when the manager can update such values.
  * ```typescript
    {
      timelineTimes: UintRange<T>[];
      
      permittedTimes: UintRange<T>[];
      forbiddenTimes: UintRange<T>[];
    }
    ```
* **TimedUpdateWithBadgeIdsPermission**: For what timelineTimes AND what badge IDs can I update the value?
  * ```typescript
    {
      timelineTimes: UintRange<T>[];
      badgeIds: UintRange<T>[];
      
      permittedTimes: UintRange<T>[];
      forbiddenTimes: UintRange<T>[];
    }
    ```
* **BalancesActionPermission**: For what badge IDs and ownedTimes can the manager execute the permission?
  * ```typescript
    {
      badgeIds: UintRange<T>[];
      ownershipTimes: UintRange<T>[];
      permittedTimes: UintRange<T>[];
      forbiddenTimes: UintRange<T>[];
    }
    ```
* **UpdateApprovedTransferPermission**: For what timeline times AND what transfer combinations (see [Representing Transfers](approvals.md)), can I update the approval criteria?
  * In the case of multiple approvals matching, we ensure that all matching approvals' criteria stays the same.
  * Ex: I cannot update the approved transfers for the transfer tuples ("All", "All", "All", 1-100, 1-10, 1-10, "All", "All") tuple, thus making the transferability locked for those transfer combinations.
    * ```typescript
      {
        fromMappingId: string;
        toMappingId: string;
        initiatedByMappingId: string;
        transferTimes: UintRange<T>[];
        badgeIds: UintRange<T>[];
        ownershipTimes: UintRange<T>[];
        approvalTrackerId: string
        challengeTrackerId: string
        
        permittedTimes: UintRange<T>[];
        forbiddenTimes: UintRange<T>[];
      }
      ```



### **Permissions vs Actual Values**

Note that the update permissions (**UpdateApprovedTransferPermission, TimedUpdatePermission, and TimedUpdateWithBadgeIdsPermission)** correspond to the UPDATABILITY of the values and are separate from the actual values themselves. The permission does not correlate to what the currently set values are.

Similarly with the **BalancesActionPermission**, it has no bearing on what the currently minted supplys are. It refers to the EXECUTABILITY of creating new badges.

**Examples**

See [Example Msgs](broken-reference) for further examples.

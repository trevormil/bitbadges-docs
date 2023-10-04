# 🔐 Permissions

First, read [Permissions](../../overview/concepts/manager.md) for an overview.

Note: The [Approved Transfers](approved-transfers.md) and [Permissions ](../../overview/concepts/manager.md)are the most powerful features of the interface, but they can also be the most confusing. For further examples, please reference the [Learn the Interface](../learn-the-interface/) section. Please ask for help if needed.



### **Permitted and Forbidden Times**

Permissions allow you to define permitted or forbidden times to be able to execute a permission.

If a permission is explicitly allowed via the permittedTimes, it will ALWAYS be allowed during those permittedTimes (can't change it).

If a permission is explicitly forbidden via the forbidden times, it will ALWAYS be disallowed during those forbiddenTimes.

If not explicitly permitted or forbidden - NEUTRAL (not defined), permissions are ALLOWED by default but can be set to be explicitly allowed or disallowed.

### **Criteria**

All permissions are stored as a linear array of (criteria -> permitted/forbiddenTimes). To check a permission, we must 1) find the corresponding match(es) using the criteria and 2) then use the corresponding permitted/forbidden times to see if it is allowed or disallowed at the CURRENT time. Note criteria matches are handled in a First-Match-Only manner.&#x20;

Ex: For timeline times 1-10, the permission is forbidden. For timeline times 1-100, the permission is allowed. In this case, the timeline times 1-10 will be forbidden because we only take the first match.

Similar to approved transfers, even though we allow range logic to be specified, we expand everything maintaining order to their singular values (one value, no ranges) before checking for matches.

### **Combinations / Default Values**

Similar to the approved transfers, we allow you to define default values and an array of combinations that manipulate (invert, override with all, or keep as is) the default values to create different combinations of permitted/forbiddenTimes. It is mainly used for shorthand and avoiding repeating values.

To match to a specific (criteria -> permitted/forbiddenTimes), we expand each combination / manipulation into a flat array maintaining order (i.e. combination 100 of element 1 comes before the first combination of element 2). We then proceed to do the first-match policy on this expanded array.

Note since we expand, empty combination arrays are meaningless. If you do not use any options, you still need to define a combination such as:

```
[
    {}
]
```

### **Permission Categories**

There are five categories of permissions, each with different criteria that must be matched with. If you get confused with the different time types, refer to [Different Time Types](different-time-fields.md) for examples and explanations.

* **ActionPermission**: Simplest (no criteria). Just denotes what times the action is executable or not.
* **TimedUpdatePermission**: For what timelineTimes, can the manager update a timeline-based value?
  * Ex: I cannot update the badge metadata values corresponding to the times 1-10, but I can for the times 10-100.
  * Note timelineTimes corresponds to the times of a timeline-based value. permittedTimes and forbiddenTimes correspond to when the manager can update such values.
* **TimedUpdateWithBadgeIdsPermission**: For what timelineTimes AND what badge IDs can I update the value?
  * Ex: I cannot update the badge metadata values for badges 1-10 corresponding to the times 1-10, but I can for the badges 11-20 for times 11-100.&#x20;
  * Note that for the criteria to match, it must satisfy BOTH the timeline times and badge IDs. So, the timeline times 11-100 and badge IDs 1-10 is not handled, and similarly, the timeline times 1-10 and badge IDs 11-20 is not defined.
* **BalancesActionPermission**: For what badge IDs and ownedTimes can the manager execute the permission?
  * Same logic as TimedUpdateWithBadgeIdsPermission but replace timelineTimes with ownedTimes.
  * Ex: Cannot create more of badge IDs 1-10 that can be owned from times 10-100. But can increase the supply of badge IDs 1-10 for after time 100.
* **UpdateApprovedTransferPermission**: For what timeline times AND what transfer combinations (see [Representing Transfers](approved-transfers.md)), can I update the approved transfer values?
  * Ex: For timeline times 1-10, I cannot update the approved transfers for the transfer tuples ("All", "All", "All", 1-100, 1-10, 1-10) tuple, thus making the transferability locked for the times 1-10 for those transfer combinations.
  * Again, note that ALL criteria must match to be handled. So, the tuple ("All", "All", "All", 1-100, <mark style="color:blue;">11-20</mark>, 1-10) is not handled because the badge IDs are different and thus non-overlapping.

### **Permissions vs Actual Values**

Note that the update permissions (**UpdateApprovedTransferPermission, TimedUpdatePermission, and TimedUpdateWithBadgeIdsPermission)** correspond to the UPDATABILITY of the values and are separate from the actual values themselves. The permission does not correlate to what the currently set values are.

Similarly with the **BalancesActionPermission**, it has no bearing on what the currently minted supplys are. It refers to the EXECUTABILITY of creating new badges.

**Examples**

See [Example Msgs](broken-reference) for further examples.

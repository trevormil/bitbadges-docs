# 🔐 Permissions

First, read [Permissions ](../../overview/how-it-works/permissions.md)for an overview.

Note: The [Approved Transfers](approved-transfers.md) and [Permissions ](../../overview/how-it-works/permissions.md)are the most powerful features of the interface, but they can also be the most confusing. For further examples, please reference the [Learn the Interface](../learn-the-interface/) section.



Permissions allow you to define permitted or forbidden times to be able to execute a permission.&#x20;

**Permitted and Forbidden Times**

If a permission is explicitly allowed via the permittedTimes, it will ALWAYS be allowed during those permittedTimes (can't change it).

If a permission is explicitly forbidden via the forbidden times, it will ALWAYS be disallowed during those forbiddenTimes.

If not explicitly permitted or forbidden, permissions are ALLOWED by default but can be set to be explicitly allowed or disallowed.

**Criteria**

All permissions are stored as a linear array of (criteria -> permitted/forbiddenTimes) maps. If the criteria matches, then we see if the permitted/forbiddenTimes allow or disallow the action.&#x20;

**IMPORTANT**: **First-Match Only**

Permissions are first-match only. If you have a \[criteriaA -> times1, criteriaA -> times2] array, times2 will always be ignored because we always take first-match only in a linear scan.&#x20;

Note that we do use ranges, but the criteria ranges can be broken down into singular value tuples. For example, (criteria = (\[1-10], \[1-10]) -> times1) can be broken down into (1, 1 -> times1), (1, 2 -> times1), ... (10,10, times1).&#x20;

So for overlapping criteria, we take the first match in a linear scan after breaking it down.

Ex: For timeline times 1-10, the permission is forbidden. For timeline times 1-100, the permission is allowed. In this case, the timeline times 1-10 will be forbidden because we take the first match.

**Permission Categories**

There are five categories of permissions, each with different criteria.

* ActionPermission: Simplest (no criteria). Just denotes what times are permitted or not.
* TimedUpdatePermission: For what timelineTimes can I update the value?
  * Ex: For timeline times 1-10, the permission is always forbidden. For timeline times 10-100, the permission is always  allowed.
* TimedUpdateWithBadgeIdsPermission: For what timelineTimes AND what badge IDs can I update the value?
  * Ex: For timeline times 1-10 and badge IDs 1-10, the permission is always forbidden.&#x20;
  * Note that for the criteria to match, it must satisfy BOTH the timeline times and Badge IDs. So, the timeline times 11-100 and badge IDs 1-10 is not defined, and the timeline times 1-10 and badge IDs 11-100 is not defined.
* BalancesActionPermission: For what badge IDs and ownedTimes can I execute the permission?
  * Used for allowing / locking the ability to create new badges, such as token unlocks
    * Can also be used in combination with the "Mint" address and collection approved transfers, if you need to also restrict the amounts that can be created.
  * Same logic as TimedUpdateWithBadgeIdsPermission but replace timelineTimes with ownedTimes.
  * Ex: Cannot create more of badge IDs 1-10 that can be owned from times 10-100. But can increase the supply of badge IDs 1-10 for after time 100.
* UpdateApprovedTransferPermission: For what timeline times AND what transfer combinations, can I update the approved transfer values?
  * Even though we use range logic, all transfers can be broken down into a (to, from, initiated by, transferTime, badgeId, ownedTime) tuple of singular addresses and values (see [Representing Transfers](approved-transfers.md)).&#x20;
  * Ex: For timeline times 1-10, I cannot update the approved transfers for the transfer tuples ("All", "All", "All", 1-100, 1-10, 1-10) tuple, thus making the transferability locked for the times 1-10 for those transfer combinations.
  * Again, note that ALL criteria must match to be handled. So, the tuple ("All", "All", "All", 1-100, <mark style="color:blue;">11-20</mark>, 1-10) is not handled because the badge IDs are different and thus non-overlapping.

**Combinations / Default Values**

TODO

**Examples**

See [Example Msgs](broken-reference) for further examples.

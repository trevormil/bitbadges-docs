# 🖌 Interface Design

Here, we explain some common types you will run into when interacting with the interface.

### Uint Ranges

A core type behind the scenes in BitBadges is the UintRange type, which simply defines a range of numbers from some start value to some end value, inclusive.&#x20;

These are typically used for representing badge IDs and time ranges. For example, transferring badges will require an UintRange\[] of badgeIds to be specified. If you say to transfer \[{ start: 1, end: 10}, {start: 20, end: 50}], it will transfer the badge IDs 1-10 and 20-50.

If used for badge IDs or times, we only allow the start and end to be within the range of 1 (**so no zero value**) to Go's math.MaxUint64 or 18446744073709551615.&#x20;

To represent a "full" or "complete" range, use \[{ start: 1, end: 18446744073709551615 }].

If we invert a range, we&#x20;

### Balances

For an overview, first read [Balances / Transfers](../../overview/how-it-works/balances-transfers.md).

IMPORTANT: If we have multiple ranges (IDs and ownership times) defined a single Balance struct, it is considered that we own ALL combinations and can be thought of as a nested for loop. There is no OR logic.&#x20;

```go
//pseudocode
ownedBalances := []*Balance{}
for _, badgeIdRange := range badgeIds {
    for _, ownedTimeRange := range ownedTimes {
        ownedBalances = append(ownedBalances, { amount,  badgeIdRange, ownedTimeRange })
    }
}
```

For example, lets say we have a balance of&#x20;

<pre class="language-json"><code class="lang-json"><strong>{ 
</strong>    amount: 1, 
    badgeIds: [{ start: 1, end: 10}, {start: 20, end: 30}], 
    ownedTimes: [{start: 20, end: 50}, {start: 100, end: 200}] 
}
</code></pre>

This can be expanded and thought of as owning:

* x1 of IDs 1-10 from times 20-50&#x20;
* x1 of IDs 1-10 from times 100-200
* x1 of IDs 20-30 from times 20-50
* x1 of IDs 20-30 from times 100-200

If we wanted to remove the first set of balances (x1 of IDs 1-10 from times 20-50), we would then have to represent it as two separate balances:&#x20;

```
{ 
    amount: 1, 
    badgeIds: [{ start: 1, end: 10}, {start: 20, end: 30}], 
    ownedTimes: [{start: 100, end: 200}] 
}
```

and

```
{ 
    amount: 1, 
    badgeIds: [{start: 20, end: 30}], 
    ownedTimes: [{start: 20, end: 50}}] 
}
```

### Address Mappings&#x20;

Address mappings are a powerful feature, similar to UintRanges. They allow us to specify a list of addresses, identified by an ID. These are invertible meaning we can create a mapping that includes all addresses EXCEPT some specified addresses. Or, we can create a mapping that includes ONLY some specified addresses.

AddressMappings are permanent and not updatable once created. The same address mapping can be referenced across collections by their unique IDs.

There are a couple IDs for AddressMappings that are reserved for efficient shorthand methods:

* If prefixed with "!", it denotes to invert the address mapping (e.g. "!id123" inverts the "id123" address mapping)
* Any valid Cosmos (bech32) address is reserved as the mapping that ONLY includes that specific address.
* "Mint" specifies the "Mint" address only.
* "Burn" specifies the "Burn" address only.
* "All" denotes all addresses (excluding the "Mint", "Burn", and "Total" addresses)
* "Manager" will be reserved for a mapping that ONLY includes the current manager of the collection

### Different Time Types

There are different time fields within the interface with different purposes:

* permittedTimes: The times that a permission can be executed successfully
* forbiddenTimes: The times that a permission is forbidden from being executed
* timelineTimes: The times that some field is a specific value for a timeline
  * Ex: X from timelineTimes 1 - 100 but changes to value Y from timelineTimes 100-200
* transferTimes: The times that a transfer can occur
* ownedTimes: The times that a user owns a badge from

**Examples**

Example 1: Let's say we have a presidential election in the US where users can vote to transfer the president badge. Lets call the time voting concludes T1 and the time the president is in charge T2 to T3. We might say that the president badge can be transferred during the time period T1 to T2 (transferTimes) for the president to own it from T2 to T3 (ownedTimes).



Example 2: Let's say we have a collection that can be optionally archived by the manager from T1 to T2 (permittedTimes) but is non-archivable at all other times (forbiddenTimes).

If the manager does archive it from T1 to T2, the isArchivedTimeline would be true from T1 to T2 and false from T2 to T3 (timelineTimes). If they do not, it would be true from T1 to T3 (timelineTimes).

### **Permissions**

First, read [Permissions ](../../overview/how-it-works/permissions.md)for an overview.

Permissions allow you to define permitted or forbidden times to be able to execute a permission.&#x20;

**Permitted and Forbidden Times**

If a permission is explicitly allowed via the permittedTimes, it will ALWAYS be allowed during those permittedTimes (can't change it).

If a permission is explicitly forbidden via the forbidden times, it will ALWAYS be disallowed during those forbiddenTimes.

If not explicitly permitted or forbidden, permissions are ALLOWED by default but can be set to be explicitly allowed or disallowed.

**Criteria**

All permissions are stored as a linear array of (criteria -> permitted/forbiddenTimes) maps. If the criteria matches, then we see if the permitted/forbiddenTimes allow or disallow the action.&#x20;

IMPORTANT: Permissions are first-match only. If you have a \[criteriaA -> times1, criteriaA -> times2] array, times2 will always be ignored because we always take first-match only in a linear scan.&#x20;

Note that we use ranges but the criteria can be broken down and expanded into singular tuples. For example, (criteria = (\[1-10], \[1-10]) -> times1) can be broken down into (1, 1 -> times1), (1, 2 -> times1), ... (10,10,times1).&#x20;

So for overlapping criteria, we take the first match in a linear scan after breaking it down.

Ex: For timeline times 1-10, the permission is forbidden. For timeline times 1-100, the permission is allowed. In this case, the timeline times 1-10 will be forbidden because we take the first match.

**Permission Categories**

There are five categories of permissions, each with different criteria.

* ActionPermission: Simplest (no criteria). Just denotes what times are allowed or not.
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
  * Even though we use range logic, all transfers can be broken down into a (to, from, initiated by, transferTime, badgeId, ownedTime) tuple of singular addresses and values.&#x20;
  * Ex: For timeline times 1-10, I cannot update the approved transfers for the transfer tuples ("All", "All", "All", 1-100, 1-10, 1-10) tuple, thus making the transferability locked for the times 1-10 for those transfer combinations.
  * Again, note that ALL criteria must match to be handled. So, the tuple ("All", "All", "All", 1-100, <mark style="color:blue;">11-20</mark>, 1-10) is not handled because the badge IDs are different and thus non-overlapping.

### Approved Transfers

First, read [Transferability ](../../overview/how-it-works/transferability.md)for an overview.

**First-Match Only**

Similar to the UpdateApprovedTransferPermission above, approved transfers are **FIRST-MATCH-ONLY**.

All approvals are a linear array of (criteria -> approval amounts and options) maps. If the criteria matches, then we use the corresponding approval amounts and options and ignore any subsequent criteria matches.

Even though we use range logic, all transfers can be broken down into a (to, from, initiated by, transferTime, badgeId, ownedTime) tuple. These tuples are used as the criteria.

By first match only, we mean that if we have ("All", "All", "All", 1-100, <mark style="color:blue;">1-10</mark>, 1-10) -> approval1 and ("All", "All", "All", 1-100, <mark style="color:blue;">1-5</mark>, 1-10) -> approval2, approval2 will never match because we only take the first match for every (to, from, initiated by, transferTime, badgeId, ownedTime) tuple.

To match, all criteria must match. So, ("All", "All", "All", 1-100, <mark style="color:blue;">1-10</mark>, 1-10) and ("All", "All", "All", 1-100, <mark style="color:blue;">11-20</mark>, 1-10) would not have any overlap.




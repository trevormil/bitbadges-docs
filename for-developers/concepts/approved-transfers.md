# 🤝 Approved Transfers

First, read [Transferability ](../../overview/how-it-works/transferability.md)for an overview of approved transfers.

Note: The [Approved Transfers](approved-transfers.md) and [Permissions ](../../overview/how-it-works/permissions.md)are the most powerful features of the interface, but they can also be the most confusing. For further examples, please reference the [Learn the Interface](../learn-the-interface/) section.





**Representing Transfers**

Even though we use range logic, we abstract and treat all transfers as broken down into (to, from, initiated by, transferTime, badgeId, ownedTime) tuples of **singular** values.&#x20;

For example, the tuples with range logic (bob, alice, bob, 10, <mark style="color:blue;">\[1-2], \[10-11]</mark>) can be broken down into smaller tuples of (bob, alice, bob, 10, <mark style="color:blue;">1, 10</mark>), (bob, alice, bob, 10, <mark style="color:blue;">2, 10</mark>), (bob, alice, bob, 10, <mark style="color:blue;">1, 11</mark>), and (bob, alice, bob, 10, <mark style="color:blue;">2, 11</mark>).&#x20;

The same can logic can be applied for breaking down address mappings with multiple addresses.

**Criteria**

Similar to the permissions above, all approved transfers are stored in a linear array of (criteria -> approval amounts and options) maps, where the criteria is a transfer tuple.

When checking if a transfer is approved, we break down both the transfers and criteria into the singular value tuples. If the tuples match, we use the corresponding approval amounts and options.

Ex: If we are checking the transfer (bob, alice, bob, 10, <mark style="color:blue;">1, 10</mark>) with the criteria (bob, alice, bob, 10, <mark style="color:blue;">\[1-2], \[10-11]</mark>), we would find a match because when broken down, they have a matching tuple. We would then use the approval amounts and options corresponding to the criteria.

If no matches are found, the transfer is considered DISALLOWED.

**First-Match Only**

Similar to the UpdateApprovedTransferPermission above, approved transfers are **FIRST-MATCH-ONLY**. Any subsequent matches are ignored.&#x20;

By first match only, we mean that if we have ("All", "All", "All", 1-100, <mark style="color:blue;">1-10</mark>, 1-10) -> approval1 and ("All", "All", "All", 1-100, <mark style="color:blue;">1-5</mark>, 1-10) -> approval2, approval2 will never be used because we there would be duplicate tuples ("All", "All", "All", 1-100, <mark style="color:blue;">1-5</mark>, 1-10) when broken down, and we would only use the first linear match which is approval1.

Also note that to match, ALL criteria must match. So, ("All", "All", "All", 1-100, <mark style="color:blue;">1-10</mark>, 1-10) and ("All", "All", "All", 1-100, <mark style="color:blue;">11-20</mark>, 1-10) would not match at all.&#x20;

**Difference From UpdateApprovedTransferPermission**

While this may seem similar to the UpdateApprovedTransferPermission, the permission corresponds to whether approved transfers can be updated or not by the manager.

The approved transfers themselves correspond to if a transfer is currently approved or not.

**User Approved Transfers**

Up until now, we have mainly talked about everything on a collection level, but each user can specify their incoming and outgoing approved transfers as well.&#x20;

These follow the exact same interface minus the following functionality:

* The "to" fields are removed for incoming and "from" fields are removed for outgoing because they will always be that user's address
* The collection level has an option to override the user-level approvals but not vice-versa

We also by default approve outgoing transfers where from equals initiatedBy (i.e. initiated by the user) and approve incoming transfers where to equals initiatedBy (see [Default User Approvals](../learn-the-interface/default-user-approvals.md)).

**Escrows**

Note that the approved transfers are just approvals and may not line up with the underlying balances. It is the approver's responsibility to make sure that enough balances are owned to not mess up the approval (including accounting for any potential revokes or freezing). This includes transfers from the "Mint" address.

For example, Alice can approve Bob to transfer x100 of badge IDs 1 - 100, but if Alice doesn't have adequate balances, Bob cannot complete the transfer. Or if Alice did have enough but some are revoked by the manager, she may not have enough at transfer time.

**Examples**

See [tutorials ](../learn-the-interface/)for further examples.

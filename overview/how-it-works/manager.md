# Manager

Each collection can optionally have a manager who can be granted special admin privileges if desired. Note that when you give up the manager role, you give up all privileges associated with it.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Permissions

* **Can delete the collection?**
* **Can archive the collection?** Archiving makes the collection read-only and rejects all transactions (until an unarchive transaction).
* **Can update core collection details?** such as metadata URLs, contract address, standard, etc.&#x20;
* **For off-chain balances, can update the balances?**
* **Transfer the role of manager?**
* **Can create more badges?**
* **Can update the transferability** aka collection's approvals ([see here](transferability.md))?

And more!

**Off-Chain Permissions**

Any tool can potentially offer custom off-chain utility exclusive to the manager as well!

### Fine-Grained Customizability

All on-chain permissions can be customized on many fine-grained levels (which badges can be updated? at what times? what values? etc.)

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**States**

There are three states that a permission can be in at any one time:

1. **Forbidden + Permanently Frozen:** This permission is forbidden and will always remain forbidden.
2. **Permitted + Not Frozen:** This permission is currently permitted but can be changed to one of the other two states.
3. **Permitted + Permanently Frozen:** This permission is forbidden and will always remain permitted

There is no forbidden + not frozen because theoretically, it could be updated to permitted at any time and executed (thus making it permitted).

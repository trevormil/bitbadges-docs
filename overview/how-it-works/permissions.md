# Permissions

Each collection can optionally have a manager who can be granted special permissions.&#x20;

Each permission can be customized to define when it is allowed vs forbidden. A permission is considered allowed by default, unless explicitly forbidden at a specific time. Once forbidden at a specific time, it cannot be unforbidden.

### **Permissions**

* **Can delete the collection?**
* **Can archive the collection?** At what times? And for what times should it be archived?
  * This makes the collection read-only and rejects all transactions (except an unarchive transaction).
* **Can update collection details?** such as metadata URLs, contract address, standard, manager role, etc. At what times? And for what times should it be specific values?
  * For details which can be specific to badge IDs (e.g. badge metadata), one can also specify which badges can be updated for what times.
* **Can create more badges?** For which badge IDs? For which ownership times ([see here](time-based-balances.md))? At what times? And what amount?
* **Can update the transferability** / collection's approved transfers ([see here](transferability.md))?

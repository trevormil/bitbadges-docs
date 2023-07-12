# Permissions

Each collection can optionally have a manager who can be granted special permissions.&#x20;

Each permission can be customized to define when it is allowed vs forbidden. A permission is considered allowed by default, unless explicitly forbidden at a specific time. Once forbidden at a specific time, it cannot be unforbidden.

### **Permissions**

* Can delete the collection?
* Can archive the collection? And for what times?
  * This makes the collection read-only and rejects all transactions (except an unarchive transaction).
* Can update the collection details such as metadata URLs, contract address, standard, etc? And for what times?
  * For details which can be specific to badge IDs (e.g. badge metadata), one can also specify which badges can be updated for what times.
* Can transfer the manager role? And for what times?
* Can create more badges? For which badge IDs? For which ownership times ([see here](balances-transfers.md))? At what times? And what amount?
* Can update the collection's approved transfers / transferability ([see here](transferability.md))?

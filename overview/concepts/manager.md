# Manager

Each collection can optionally have a manager who can be granted special admin privileges if desired.&#x20;

IMPORTANT: Note that when you give up the manager role, you give up all privileges associated with it.

### Permissions (Blockchain)

* **Can delete the collection?**
* **Can archive the collection?** At what times? And for what times should the collection be archived?
  * Archiving makes the collection read-only and rejects all transactions (except an unarchive transaction).
* **Can update collection details?** such as metadata URLs, contract address, standard, etc. At what times? And for what times should it be specific values?
  * For details which can be specific to badge IDs (e.g. badge metadata), one can also specify which badges can be updated for what times.
* **For off-chain balances, can update the balances URL?**
* **Transfer the role of manager?**
* **Can create more badges?** For which badge IDs? For which ownership times ([see here](time-based-balances.md))? At what times? And what amount?
* **Can update the transferability** / collection's approved transfers ([see here](transferability.md))?
* **Can revoke badges?**
* **Can freeze users from transferring?**&#x20;
* **Can freeze badges from being transferred?**

And more!



### **BitBadges Website Privileges**

If you are the manager of the collection, you can do the following:

* **Send an announcement to all badge holders**
* **View the secret codes and passwords for all code / password based claims**



### Other

Anyone can create and offer custom utility exclusive to the manager!

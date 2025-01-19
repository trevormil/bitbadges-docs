# Manager

In the BitBadges ecosystem, each badge collection can optionally have a designated manager or administrator. This role comes with special administrative privileges that can be customized according to the collection's needs.

It's important to note that relinquishing the manager role means giving up all associated privileges.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (4).png" alt=""><figcaption><p>Manager Dashboard Overview</p></figcaption></figure>

## Manager Permissions

The manager role can be granted various permissions, allowing for flexible administration of the collection. These permissions include, but are not limited to:

1. **Collection Deletion:** The ability to permanently remove the collection from the system.
2. **Collection Archiving:** Managers can archive a collection, making it read-only and rejecting all transactions until an unarchive action is performed.
3. **Core Collection Updates:** This includes modifying essential details such as metadata URLs, contract addresses, and the collection's standard.
4. **Off-chain Balance Management:** For collections using off-chain balance storage, managers can update these balances.
5. **Manager Role Transfer:** The ability to pass the manager role to another address.
6. **Badge Creation:** Managers can be given permission to mint additional badges within the collection.
7. **Transferability Updates:** This involves modifying the collection's approval settings, which determine how badges can be transferred. (For more details, see [transferability](transferability.md))
8. **Custom Permissions:** Depending on the collection's setup, there may be additional, collection-specific permissions available to the manager.

### Off-Chain Permissions

It's worth noting that the manager role can extend beyond on-chain functionalities. Various tools and platforms can offer custom off-chain utilities exclusive to the manager, further expanding the role's capabilities and responsibilities.

## Fine-Grained Customizability

One of the key features of the manager role in BitBadges is the ability to customize permissions at a granular level. This allows for precise control over the collection's management.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Fine-Grained Permission Settings</p></figcaption></figure>

Permissions can be customized based on various factors, including:

* **Badge Specificity:** Which particular badges within the collection can be affected?
* **Time Constraints:** When can certain actions be performed?
* **Value Limitations:** What specific values or ranges are allowed for updates?
* **Conditional Triggers:** Under what circumstances can certain permissions be exercised?

This level of customization allows collection creators to implement complex management strategies tailored to their specific needs.

## Permission States

Each permission can exist in one of three states:

1. **Forbidden + Permanently Frozen:**
   * The permission is permanently disallowed.
   * This state cannot be changed, ensuring certain actions remain off-limits indefinitely.
2. **Permitted + Not Frozen:**
   * The permission is currently allowed.
   * This state can be changed to either of the other two states, offering flexibility in management.
3. **Permitted + Permanently Frozen:**
   * The permission is permanently allowed.
   * Like the first state, this cannot be changed, ensuring certain capabilities always remain available.

It's important to note that there is no "Forbidden + Not Frozen" state. This is because such a state could theoretically be updated to "Permitted" at any time and then immediately executed, effectively making it a "Permitted" state.

This system of states allows for both flexibility and security in managing collection permissions, enabling creators to set up governance structures that can evolve over time or remain fixed, depending on their needs.

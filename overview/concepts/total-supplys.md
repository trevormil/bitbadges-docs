# Total Supplys

Each badge in a collection will have its own circulating supply. When initially created, all badges will be sent to a special "Mint" address. From there, they can be distributed to users.

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>Example distribution</p></figcaption></figure>

### **Fungible vs Non-Fungible vs Semi-Fungible**

You may have heard the terms fungible and non-fungible tokens (NFTs). Fungible means that there is only one badge in a collection with X supply. Non-fungible means that there is multiple badges in a collection, all with a supply of 1.

BitBadges doesn't necessarily categorize them by these names because we use a semi-fungible standard where each badge ID can have a custom supply.

### **Creating Badges**

Badges can be created by the manager of the collection, according to the "can create more?" permission set. This permission can be customized to permit or forbid the creation of a certain amount of badges at certain times. If the creation of all badges is locked forever, the total supply is final.

Created badges are sent to the "Mint" address initially and distributed from there.

### **Burning Badges**

Badges cannot be "burned" once created. However, they can be transferred (if allowed) to the addresses where no private key is known such as the Ethereum zero address.

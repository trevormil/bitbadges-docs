# Total Supplys

Each badge in a collection will have its own circulating supply.&#x20;

Typically, when initially created, all badges will be sent to a special "Mint" address. From there, they can be distributed to users. We also offer a default starting balances option, meaning that users will start out with xN balance upon first interaction with the collection. These can be used in combination with one another, but they are typically used separately.

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption><p>Example distribution</p></figcaption></figure>

### **Fungible vs Non-Fungible vs Semi-Fungible**

You may have heard the terms fungible and non-fungible tokens (NFTs). Fungible means that there is only one badge in a collection with X supply. Non-fungible means that there is multiple badges in a collection, all with a supply of 1.

BitBadges doesn't necessarily categorize them by these names because we use a semi-fungible standard where each badge ID can have a custom supply.

### **Creating Badges**

**Option 1: Limited Supply**

Badges can be created "out of thin air" by the manager of the collection, according to the "can create more?" permission set. This permission can be customized to permit or forbid the creation of a certain amount of badges at certain times. If the creation of all badges is locked forever, the total supply is final. Created badges are sent to the "Mint" address initially and distributed from there.

**Option 2: Default Starting Balances**

Upon creation of the collection, a default starting balance can be set for all users. Upon first interaction, ALL addresses will be given these default balances. However, because ALL addresses receive these, note that there could potentially be an infinite supply of badges.

### **Restricting Supply**

There are two options to restrict the circulating supply. These can be used in combination, but again, they are typically not.

**Option 1: Can Create More? Permission**

The "can create more?" permission set defines if badges can be created out of thin air at any given time by the manager. If they can, there is **no limit** to how many they create. For example, the permission might specify that the manager can create more of badge ID 11 after January finishes.

**Option 2: Restricting with Transferability**

The other option is to use the collection-level transferability to lock the transfer flow and use the Mint address as an escrow. This allows you to be a little more fine-grained over the circulating supply and recipients of badges, whereas Option 1 is can create an unlimited amount or none at all.

For example, lets say you create x500 of a badge at creation time and lock the ability to create more. Using the transferability, you could enforce that x100 is unlocked this year, x100 is unlocked next year, and so on, for example. You could also restrict who it is unlocked to.

### **Burning Badges**

Badges cannot be "burned" once created. However, they can be transferred (if allowed) to addresses where no private key is known such as the Ethereum zero address.

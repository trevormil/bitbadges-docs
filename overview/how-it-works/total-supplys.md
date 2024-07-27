# Total Supplys

Each badge in a collection will have its own circulating supply. For badges, you can think of the "Mint" address having unlimited balances. All circulating supply originates from a transfer from the Mint address.

### **Fungible vs Non-Fungible vs Semi-Fungible**

You may have heard the terms fungible and non-fungible tokens (NFTs). Fungible means that there is only one badge in a collection with X supply. Non-fungible means that there is multiple badges in a collection, all with a supply of 1.

BitBadges doesn't necessarily categorize them by these names because we use a semi-fungible standard where each badge ID can have a custom supply.

### **Restricting Supply**

As mentioned above, the Mint address has unlimited balances. To restrict supply, you must restrict the approvals from the Mint address. This allows you to be fine-grained over the circulating supply and recipients of badges.

For example, lets say you create x500 of a badge at creation time and lock the ability to create more. Using the transferability, you could enforce that x100 is unlocked this year, x100 is unlocked next year, and so on, for example. You could also restrict who it is unlocked to.

The updatability of the approvals is also important. This is managed by the amnager. They can lock specific approvals, specific badges, and so on on a super fine grained level.

### **Burning Badges**

Badges cannot be "burned" once created. However, they can be transferred (if allowed) to addresses where no private key is known such as the Ethereum zero address.

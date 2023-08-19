# Balances Types

BitBadges offers three different ways to store the badge balances and owners for your collection, each with their own pros and cons.

### Standard

Standard balances are what you may be familiar with. All balances are stored on the blockchain, and users can transfer badges to each other by transacting with the blockchain. This is the most customizable option but also the least scalable because it uses the most blockchain resources.

### Inherited / Badge-Bound

Inherited (or badge-bound) balances are a way for a collection to inherit the owners of badges from another collection. For example, I want to send a free coupon badge which automatically distributes to all owners of my membership badge.&#x20;

If the "parent" badge transfers owners, all child badges will transfer owners.

### Off-Chain

Off-chain balances are a new scalable way to store balances for your collection with enhanced user experience but more centralization and less flexibility.

No transfers or approvals EVER occur on the blockchain. All balances will be stored off-chain (via a typical server or file storage like IPFS) and dynamically fetched from the storage's URL. Whatever is returned from the URL when fetched is what the allocated balances at that time are.

The URL can be set to be non-updatable and/or use permanent storage like IPFS (will always return the same balances). If both are used, the balances will be frozen and permanent and will never change.



**Benefits**

**This architecture reduces the resources used by your collection by >99%** because there are no transfer transactions being executed on-chain. This also means your **users never have to transact directly with the blockchain and pay gas fees!**



**Drawbacks**

However, it is a trade-off between scalability and user experience vs functionality and decentralization. The blockchain has no control over what is returned by the URL (which can introduce a centralized trust factor).

And since there are no on-chain transfers, certain functionality (such as approvals, customizable transferability, and claims) cannot be used.



**Should my collection use off-chain balances?**

Ultimately, that is up to you to decide based on the tradeoffs.

However, we observe there are two main types of collections often benefit most from this. If your collection fits one of these criteria, off-chain balances might be beneficial.

1. **Non-Transferable / Soulbound -** Badges in your collection are never meant to be transferred and are always tied to specific users.&#x20;
2. **Centralized Control Over Allocation** - There is a single entity that is supposed to or can have complete control over the allocation of badges at all times.



**Can updating balances be programmed?**

Yes! As mentioned above, if not using permanent storage, you can program your URL to return whatever balances you want, so you can implement your own custom logic! See [here](../../for-developers/tutorials/create-a-collection-with-off-chain-balances.md).

Find a tool or tutorial for your use case on the [Ecosystem ](../ecosystem.md)page!



**Why not just use a standard client-server solution?**

There are many benefits over a standard solution. Just to name a few:

* Creation, maintenance, and verification of the badges is outsourced leaving you with less work
* Access and seamless integration with the whole suite of BitBadges tools
* Enhanced security, verification, and availability of badges due to the collection still being created on the blockchain.
* Allow users to build their digital identity all in one place, rather than scattered across many different websites


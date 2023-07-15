# Balances Types

BitBadges offers three different ways to store the badge balances and owners for your collection, each with their own pros and cons.

### Standard

Standard balances are what you may be familiar with. All balances are stored on the blockchain, and users can transfer badges to each other by transacting with the blockchain. This is the most customizable option but also the least scalable because it uses the most blockchain resources.

### Inherited / Badge-Bound

Inherited (or badge-bound) balances are a way for a collection to inherit the owners of badges from another collection. For example, I want to send a free coupon badge which automatically distributes to all owners of my membership badge.&#x20;

If the "parent" badge transfers owners, all child badges will transfer owners.

### Off-Chain

Off-chain balances are a new scalable way to store balances for your collection with enhanced user experience. With off-chain balances, the collection is still created on the blockchain (e.g. metadata is defined, total supplys are defined), but all balances of badges for the collection are stored off the blockchain and fetched by a URL (via a typical server or file storage like IPFS). Note that verifiability is not sacrificed, as long as the balances at the URL can be fetched.

You have complete control over the balances that are returned by the URL. For example, a ticketing company can update the returned balances for the ticket owner badge whenever one is bought, all **without transacting with the blockchain**! Or, you can permanently freeze the balances, making the badges non-transferable (see below).

**This architecture reduces the resources used by the blockchain by >99%** because there are no transfer transactions being executed on-chain. This means your **users never have to transact directly with the blockchain and pay gas fees!**&#x20;

However, it is a trade-off between scalability and user experience vs functionality and decentralization. The blockchain has no control over what is returned by the URL (which introduces a centralized trust factor if balances are not frozen). And since there are no on-chain transfers, certain functionality (such as approvals, customizable transferability, and more) cannot be used.

**Can updating balances be made automatic?**

Yes! As mentioned above, you can program your balances to update automatically however you would like. Find a tool or tutorial for your use case on the [Ecosystem ](../ecosystem.md)page!

**Why not just use a standard client-server solution?**

There are many benefits to creating and using the off-chain balances type. Just to name a few:

* Creation, maintenance, and verification of the badges is outsourced leaving you with less work
* Access and seamless integration with the whole suite of BitBadges tools
* Enhanced security, verification, and availability of badges due to blockchain technology
* Allow users to build their digital identity all in one place, rather than scattered across many different websites

**How are the balances permanently frozen?**

The balances URL can be set to non-updatable via the permissions. and if the URL is hosted via a permanent file storage solution like IPFS, then the balances will be permanently frozen (never change) but always verifiable.

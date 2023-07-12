# Balances Types

BitBadges offers three different ways to store the badge balances and owners for your collection.

### Standard

Standard balances are what you may be familiar with. All balances are stored on the blockchain, and users can transfer badges to each other by transacting with the blockchain. This is the most customizable option but also the least scalable because it uses the most blockchain resources.

### Inherited / Badge-Bound

Inherited (or badge-bound) balances are a way for a collection to inherit the owners of badges from another collection. For example, I want to create a free coupon badge which automatically distributes to all users who own a membership badge.&#x20;

If your collection uses inherited balances, users cannot transfer or approve badges in your collection. To receive or send badges, they must send or receive the badges in the parent collection (which would then automatically populate down and send / receive the badges in your collection).&#x20;

### Off-Chain

Off-chain balances are a new scalable way to store balances for your collection with enhanced user experience. We envision this to be the most popular option.

The collection is still created on the blockchain, but all balances are stored off the blockchain and referenced by a simple URL (via a typical server or file storage like IPFS).&#x20;

This option is much more limited in terms of functionality because **users cannot transfer or approve any badges on the blockchain**. **Depending on the permissions set, the balances can only be 1) completely frozen forever or 2) completely controllable by the manager of the collection (thus adding a trust factor).**&#x20;

Overall, it is a tradeoff between scalability and user experience vs functionality and decentralization.

However, almost all digital tokens used today follow this format. For example, distribution of verification checkmarks are completely controlled by the social media company and non-transferable. Distribution of concert tickets are completely controlled by the ticketing company. Attendance details are frozen forever.

**Can updating balances be made automatic?**

Yes! You can program your balances to update automatically however you would like. For example, whenever a user signs up on your website, you can send a sign-up badge to that user, **all without transacting with the blockchain** (see below)!

Find a tool or tutorial for your use case on the [Ecosystem ](../ecosystem.md)page!

**Do users or the manager need to transact with the blockchain to update balances?**

No, users will never have to transact with the blockchain to send / receive badges, which is why the user experience is so enhanced.

The manager will have to transact with the blockchain if they need to change the balances URL. However, this is most often not the case because the balances stored at the URL can be updated without having to update the URL.

**Does this sacrifice verifiability?**

The collection is still created on the blockchain (with the off-chain balances URL). As long as the balances at the URL can be fetched, the collection will not sacrifice any verifiability.

**Why not just use a standard client-server solution if balances are stored off-chain?**

There are many benefits to creating and using BitBadges to implement off-chain tokens. Just to name a few:

* Creation, maintenance, and verification is outsourced leaving you with less work
* Access and seamless integration with the whole suite of BitBadges tools
* Enhanced security, verification, and availability of badges due to blockchain technology
* Allow users to build their digital identity all in one place, rather than scattered across many different websites

**How are the balances permanently frozen?**

The balances URL can be set to non-updatable. If the URL is hosted via a permanent file storage solution like IPFS, then the balances will be permanently frozen (never change) and always verifiable.

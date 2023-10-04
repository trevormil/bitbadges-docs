# 🪙 Balance Types

BitBadges offers three different ways to store the badge balances and owners for your collection as explained [here](../../overview/concepts/balances-types.md). Please read this first.

The balance types are "Standard", "Off-Chain", and "Inherited". The balances type for a collection is determined by the **balancesType** field of the collection which will either be "Standard", "Off-Chain", and "Inherited".

### Standard

With standard balances, all features of the interface are supported.&#x20;

### Off-Chain

With off-chain balances, you will create a new collection on-chain and will define details unique to this created collection such as badge metadata, standards, etc. You will also create the badges on-chain to define a verifiable maximum total supply (even though they will permanently live in the "Mint" account). **However, all transfers and approval transactions will throw an error if attempted because these are facilitated off-chain.**

Balances, at any given time, will be queried from a URI (stored via the **offChainBalancesMetadata** field of the collection). When querying the balances from the URI, the querier should verify that the maximum supply is not exceeded (i.e. the provider is not overallocating badges). We throw errors in the BitBadges indexer if this happens.

**How are the balances permanently frozen for off-chain collections?**

The balances URL can be set to non-updatable via the manager permissions, and if the URL is hosted via a permanent file storage solution like IPFS, then the balances will be permanently frozen (never change) but always verifiable.&#x20;

This is because the IPFS URIs are hash-based. So if the hash is permanently stored and frozen on-chain and the balances file is known, you can always verify the hash.

**How are balances refreshed on the BitBadges indexer for unfrozen off-chain collections?**

First, the URI must be set to return the updated balances. Then, balances can be refreshed on the BitBadges indexer by triggering the refresh API endpoint.

#### Custom Logic Implementation

Off-chain balances' updatable nature allows for the implementation of custom logic for what is returned by the URL. This empowers you to define and program your balance-fetching process to align with your collection's unique requirements.&#x20;

For example, you can dynamically revoke and assign based on if users pay their subscription fees for a month all without ever interacting with the blockchain (since the URL won't change).

See [here](../tutorials/create-a-collection-with-off-chain-balances.md). Or, find a tool or tutorial for your use case on the [Ecosystem ](../../overview/ecosystem.md)page!

### Inherited / Badge-Bound (Coming Soon)

For collections with inherited balances, you can create a new collection and can define details unique to this created collection such as badge metadata, standards, etc.&#x20;

**However, all transfers, approval transactions, and any badge creation transactions will throw an error if attempted.** This is because all of these properties are inherited from the "parent" collection, so they should not be defined in the current collection.

To specify where to inherit from, use the **inheritedBalancesTimeline** field of the collection.


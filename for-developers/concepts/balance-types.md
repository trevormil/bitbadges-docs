# 🪙 Balance Types

BitBadges offers three different ways to store the badge balances and owners for your collection as explained [here](../../overview/how-it-works/balances-types.md). These are "Standard", "Off-Chain", and "Inherited" (aka badge bound). The balances type for a collection is determined by the **balancesType** field of the collection which will either be "Standard", "Off-Chain", and "Inherited".

### Standard

With standard balances, all features are supported.&#x20;

### Inherited / Badge-Bound

For collections with inherited balances, you will create a new collection and can define details unique to this created collection such as badge metadata, standards, etc.&#x20;

However, all transfers, approval transactions, and any badge creation transactions will throw an error if attempted. This is because all of these are inherited from the "parent" collection, so they should not be defined in the current collection.

To specify where to inherit from, use the **inheritedBalancesTimeline** field of the collection.

### Off-Chain

Similarly with off-chain balances, you will create a new collection and can define details unique to this created collection such as badge metadata, standards, etc. All transfers and approval transactions will throw an error if attempted because these are facilitated off-chain.

However, you MUST create the badges on-chain to define a verifiable maximum total supply (even though they will permanently live in the "Mint" account).&#x20;

When querying the balances from the URI, the querier should verify that the maximum supply is not exceeded. We throw errors in the BitBadges indexer if this happens.

To specify the off-chain balances metadata, use the **offChainBalancesMetadata** field of the collection.

**How are the balances permanently frozen for off-chain collections?**

The balances URL can be set to non-updatable via the permissions, and if the URL is hosted via a permanent file storage solution like IPFS, then the balances will be permanently frozen (never change) but always verifiable.&#x20;

This is because the IPFS URIs are hash-based. So if the hash is permanently stored and frozen on-chain and the balances file is known, you can always verify the hash.

**How are balances refreshed for unfrozen off-chain collections?**

First, the URI must return the updated balances. Then, balances can be refreshed on the BitBadges indexer by triggering the refresh API endpoint.

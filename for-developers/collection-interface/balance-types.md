# 🪙 Balance Types

BitBadges offers different ways to store the badge balances and owners for your collection as explained [here](../../overview/concepts/balances-types.md). Please read this first.The balances type for a collection is determined by the **balancesType** field of the collection which.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Standard

```json
"balancesType": "Standard"
```

With standard balances, all features of the interface are supported, and everything is facilitated on-chain. Badges are initially sent to the "Mint" address and can be transferred from there.

## Off-Chain

With off-chain balances, you will create a new collection on-chain and will define details unique to this created collection such as badge metadata, standards, etc. **However, all transfers and approval transactions will throw an error if attempted because these are to be facilitated off-chain.**

Balances, at any given time, will be queried from the specified URI (stored via the **offChainBalancesMetadata** field of the collection).&#x20;

```json
"offChainBalancesMetadataTimeline": [
  {
    "timelineTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "offChainBalancesMetadata": {
      "uri": "ipfs://QmXZTfhNAPJ32WsBaRRDSwxok9phVisCcDy8asBvcmBzsR",
      "customData": ""
    }
  }
]
```

#### Benefits

* **Significant Resource Reduction**: The architecture's off-chain nature results in a substantial reduction of resources used by your collection—potentially up to over 99%. This is primarily due to the absence of on-chain transfer transactions and balances.
* **No-Cost Updates:** If the balances URL (stored on-chain) remains the same, balances can be updated by simply editing the JSON returned from the server. This means balances can be updated without interacting with the blockchain and paying transaction fees.
* **Enhanced User Experience**: Users are relieved from the need to interact directly with the blockchain and incur gas fees. This streamlined user experience enhances accessibility and usability. Badges are automatically populated into a user's portfolio without the user ever executing a blockchain transaction.

#### Drawbacks

* **Scalability vs. Functionality Trade-off**: While off-chain balances offer scalability and user-centric benefits, they entail trade-offs in terms of functionality and decentralization. Mainly, \
  since there are no on-chain transfers, certain functionality (such as approvals, customizable transferability, and claims) is not supported, unless custom implemented off-chain.
* **Centralized Trust Factor**: The URL-driven approach introduces a centralized trust element, as the blockchain has no control over the data returned by the URL or the assignment of the balances.

#### Suitability of Off-Chain Balances

We envision collections adopting off-chain balances if they align with one of the following criteria:

1. **Non-Transferable / Soulbound**: If your collection's badges are intrinsically tied to specific users and are not intended for transfer ever, off-chain balances could make your collection scalability without sacrificing verifiability or centralization.
2. **Centralized Allocation Control**: In cases where a single entity should maintain complete control over badge allocation (concert tickets, diplomas, etc), the off-chain approach can be particularly beneficial.&#x20;

#### Advantages Over Standard Solutions

Compared to traditional client-server solutions, off-chain balances offer numerous advantages, including:

* **Simplified Badge Management**: Outsourcing badge creation, maintenance, and verification reduces your workload.
* **Seamless Integration**: Integration with the complete suite of BitBadges tools.
* **Enhanced Security and Availability**: While balances are off-chain, the collection's core creation and foundation remain on the blockchain where it benefits from security, immutability, and availability.
* **Unified Digital Identity Building**: Users can consolidate their digital identity to their single address, eliminating fragmentation across various websites.

In conclusion, off-chain balances present an intriguing avenue to enhance scalability, user experience, and badge management. While there are considerations and trade-offs, the decision to adopt this approach hinges on your collection's specific goals and priorities. For additional resources and guidance, consult the Ecosystem page to identify suitable tools and tutorials for your use case.

**How are balances permanently frozen?**

The balances URL can be set to non-updatable via the manager permissions (**canUpdateOffChainBalancesMetadata**), and if the URL is hosted via a permanent file storage solution like IPFS, then the balances will be permanently frozen (never change) but always verifiable.&#x20;

This is because the IPFS URIs are hash-based. So if the hash is permanently stored and frozen on-chain and the balances file is known, you can always verify the hash.

```json
"canUpdateOffChainBalancesMetadata": [
  {
    "timelineTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "permittedTimes": [],
    "forbiddenTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ]
  }
]
```

#### Custom Logic Implementation

Off-chain balances' updatable nature allows for the implementation of custom logic for what is returned by the URL (if not using a permanent URL). This empowers you to define and program your balance assignment process to align with your collection's unique requirements.&#x20;

For example, you can dynamically revoke and assign based on if users pay their subscription fees for a month all without ever interacting with the blockchain (since the URL won't change). You simply need to just update the JSON map returned.

### Indexed vs Non-Indexed

Off-chain balances can either be indexed or non-indexed. The differences are as follows:

* Indexed balances have a total verifiable supply defined on-chain. Non-indexed does not.
* At any time, for indexed balances, all owners and their balances are known. With non-indexed, this is not tracked, and we fetch on-demand from the source every time.
* For indexed balances, a ledger of activity is tracked. For non-indexed, there is no ledger kept. You can only view the current balances at any given time.
* Indexed balances will show up in standard search results like user's portfolios. For non-indexed, you have to check it manually.&#x20;
* Indexed balances have a limit of unique owners (set by the indexer) for scalability reasons, whereas non-indexed has no such limit.

### Off-Chain - Indexed Balances

<pre class="language-json"><code class="lang-json"><strong>"balancesType": "Off-Chain - Indexed"
</strong></code></pre>

There are two options when creating off-chain balances: "Off-Chain - Indexed" and "Off-Chain - Non-Indexed".

For indexed balances, indexers are expected to do and know the following:

* Keep track of ALL owners and their balances at any given time
* Create a ledger of activity for any balance updates

To facilitate this, at any time, we must:

* Know the expected total supply which is verifiable on-chain
* Be able to query ALL balances at any given time.

We do this by the following:

* The badges that live in the "Mint" address is considered the total verifiable maximum supply. Since no transfers are allowed on-chain, they will permanently live in the "Mint" address once created. When querying the balances from the URI, the querier should verify that the maximum supply defined on-chain (the badge balance of the "Mint" address) is not exceeded (i.e. the provider is not overallocating badges). We throw errors in the BitBadges indexer if this happens.
* Each balances query will return a map of ALL balances for the collection. This is done via a JSON of user -> balance definitions. See below.

**What format should the balances be in?**&#x20;

To facilitate the expected functionality of indexed balances, the returned balances are expected to be in a specific format. It should be a JSON object where the keys are Cosmos addresses / address mapping IDs and the values are Balance\<string>\[]. See [https://bafybeiejae7ylsndxcpxfrfctdlzh2my7ts5hk6fxhxverib7vei3wjn4a.ipfs.dweb.link/](https://bafybeiejae7ylsndxcpxfrfctdlzh2my7ts5hk6fxhxverib7vei3wjn4a.ipfs.dweb.link/).

Note that if you use address mapping IDs for the keys ([see here to learn more](../concepts/address-mappings-lists.md)), the corresponding address mapping must be a whitelist (includeAddresses = false) and stored on-chain for reproducability (not off-chain via the BitBadges servers or somewhere else).

See [here](../bitbadges-sdk/common-snippets/off-chain-balances.md) for further info using the SDK for off-chain balances.

**How are balances refreshed on the BitBadges indexer for unfrozen off-chain collections?**

To refresh an indexer, a refresh needs to be triggered because off-chain indexed balances are cached.

To refresh, first, the URI must be set to return the updated balances. Then, balances can be refreshed on the BitBadges indexer by triggering the refresh API endpoint.&#x20;

The BitBadges indexer auto triggers an update if the on-chain URL is updated.

#### Custom Logic Implementation

Example:

```typescript
app.get('/api/v0/airdrop/balances', async (req, res) => {
  const allAirdropped = await AIRDROP_DB.list();
  const airdropped = allAirdropped.rows.map(row => row.id);
  const balancesMap: OffChainBalancesMap<bigint> = {};
  for (const address of airdropped) {
    balancesMap[address] = [{
      amount: 1n,
      badgeIds: [{
        start: 1n, end: 1n,
      }],
      ownershipTimes: [{
        start: 1n, end: 18446744073709551615n,
      }],
    }
    ];
  }
  return res.send(balancesMap);
});
```

This dynamically updates what balances are returned from the URL based on who has received an airdrop or not (using a private airdrop database). This is all done off-chain, meaning balances are updated without a blockchain transaction (i.e. the on-chain URL stays the same as API\_URL/api/v0/airdrop/balances)/

For another tutorial, see [here](../tutorials/create-and-host-off-chain-balances.md). Or, find a tool or tutorial for your use case on the [Ecosystem ](../../overview/ecosystem.md)page!

### Off-Chain - Non-Indexed

```json
"balancesType": "Off-Chain - Non-Indexed"
```

Non-indexed balances are the same as indexed balances, but there is no verifiable total supply defined on-chain. Balances are not indexed, meaning they are expected to be dynamically fetched on-demand, and there is no activity ledger recorded because of this.

At any given time, we do not know the entire list of owners and balances, unlike with indexed balances. As a result, we do not show these balances in search results like a user's portfolio. The only way to query balances for a user is to manually query it.

The benefit of this is that this approach is much more scalable to indexers. Indexers do not need to worry about caching and maintaining logs of balances. As a result, there is no limit on the number of unique owners, whereas there is a limit (currently 15000 for the BitBadges API / Indexer) for standard indexed balances.

All URIs must contain the "{address}" placeholder in the on-chain specified URI. This is to be replaced at fetch time with the user's Cosmos address. it is up to you whether you want to support native chain addresses as well, but the converted Cosmos address is a minimum requirement.

The return value from the URI should be dynamic in the following format:&#x20;

```
{ balances: [....] }
```

Standard indexed balances return the whole map of user -> balances whereas non-indexed only returns the requested user's balances.&#x20;

```json
"offChainBalancesMetadataTimeline": [
  {
    "timelineTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "offChainBalancesMetadata": {
      "uri": "https://api.bitbadges.io/balances/{address}",
      "customData": ""
    }
  }
]
```

The created badges on-chain (the ones sent to the Mint address via **badgesToCreate**) are only used for the expected badge IDs of the collection.

#### Custom Logic Implementation

Example:

On-Chain URL: "http://localhost:3000/nonIndexed/{address}"

```typescript
app.get('/nonIndexed/:address', async (req, res) => {
  const address = req.params.address; 
  const cosmosAddress = convertToCosmosAddress();
  
  //custom logic

  const balances: Balance<bigint>[] = [...];
  return res.status(200).send({ balances });
});
```

For a full tutorial, see [here](../tutorials/create-and-host-off-chain-balances.md).  Or, see [here](https://github.com/BitBadges/bitbadges-indexer/blob/master/src/routes/ethFirstTx.ts) for how we implement the first Ethereum tx badge.

Or, find a tool or tutorial for your use case on the [Ecosystem ](../../overview/ecosystem.md)page!

### Inherited / Badge-Bound (Coming Soon)

For collections with inherited balances, you can create a new collection and can define details unique to this created collection such as badge metadata, standards, etc.&#x20;

**However, all transfers, approval transactions, and any badge creation transactions will throw an error if attempted.** This is because all of these properties are inherited from the "parent" collection, so they should not be defined in the current collection.

To specify where to inherit from, use the **inheritedBalancesTimeline** field of the collection.


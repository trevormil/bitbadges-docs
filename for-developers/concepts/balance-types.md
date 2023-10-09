# 🪙 Balance Types

BitBadges offers three different ways to store the badge balances and owners for your collection as explained [here](../../overview/concepts/balances-types.md). Please read this first.

The balance types are "Standard", "Off-Chain", and "Inherited". The balances type for a collection is determined by the **balancesType** field of the collection which will either be "Standard", "Off-Chain", and "Inherited".

### Standard

With standard balances, all features of the interface are supported, and everything is facilitated on-chain.

### Off-Chain

With off-chain balances, you will create a new collection on-chain and will define details unique to this created collection such as badge metadata, standards, etc. You will also create the badges on-chain to define a verifiable maximum total supply (even though they will permanently live in the "Mint" account). **However, all transfers and approval transactions will throw an error if attempted because these are to be facilitated off-chain.**

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

When querying the balances from the URI, the querier should verify that the maximum supply defined on-chain (the badge balance of the "Mint" address) is not exceeded (i.e. the provider is not overallocating badges). We throw errors in the BitBadges indexer if this happens.

#### Benefits

* **Significant Resource Reduction**: The architecture's off-chain nature results in a substantial reduction of resources used by your collection—potentially up to over 99%. This is primarily due to the absence of on-chain transfer transactions and balances.
* **No-Cost Updates:** If the balances URL (stored on-chain) remains the same, balances can be updated by simply editing the JSON returned from the server. This means balances can be updated without interacting with the blockchain and paying transaction fees.
* **Enhanced User Experience**: Users are relieved from the need to interact directly with the blockchain and incur gas fees. This streamlined user experience enhances accessibility and usability. Badges are automatically populated into a user's portfolio without the user ever executing a blockchain transaction.

#### Drawbacks

* **Scalability vs. Functionality Trade-off**: While off-chain balances offer scalability and user-centric benefits, they entail trade-offs in terms of functionality and decentralization. Mainly, \
  since there are no on-chain transfers, certain functionality (such as approvals, customizable transferability, and claims) is not supported, unless custom implemented off-chain.
* **Centralized Trust Factor**: The URL-driven approach introduces a centralized trust element, as the blockchain has no control over the data returned by the URL or the assignment of the balances.

### Suitability of Off-Chain Balances

#### Criteria for Adoption

Consider adopting off-chain balances if your collection aligns with the following criteria:

1. **Non-Transferable / Soulbound**: If your collection's badges are intrinsically tied to specific users and are not intended for transfer, off-chain balances could be advantageous, especially if you make the balances frozen and immutable.
2. **Centralized Allocation Control**: In cases where a single entity should maintain complete control over badge allocation (concert tickets, diplomas, etc), the off-chain approach can be particularly beneficial.

#### Advantages Over Standard Solutions

Compared to traditional client-server solutions, off-chain balances offer numerous advantages, including:

* **Simplified Badge Management**: Outsourcing badge creation, maintenance, and verification reduces your workload.
* **Seamless Integration**: Integration with the complete suite of BitBadges tools.
* **Enhanced Security and Availability**: While balances are off-chain, the collection's core creation and foundation remain on the blockchain where it benefits from security, immutability, and availability.
* **Unified Digital Identity Building**: Users can consolidate their digital identity to their single address, eliminating fragmentation across various websites.

In conclusion, off-chain balances present an intriguing avenue to enhance scalability, user experience, and badge management. While there are considerations and trade-offs, the decision to adopt this approach hinges on your collection's specific goals and priorities. For additional resources and guidance, consult the Ecosystem page to identify suitable tools and tutorials for your use case.

**What format should the balances be in?**&#x20;

It should be a JSON object where keys are Cosmos addresses and the values are Balance\<string>\[]. See [https://bafybeiejae7ylsndxcpxfrfctdlzh2my7ts5hk6fxhxverib7vei3wjn4a.ipfs.dweb.link/](https://bafybeiejae7ylsndxcpxfrfctdlzh2my7ts5hk6fxhxverib7vei3wjn4a.ipfs.dweb.link/).

See [here](../../sdk/common-snippets/off-chain-balances.md) for further info using the SDK for off-chain balances.

**How are the balances permanently frozen for off-chain collections?**

The balances URL can be set to non-updatable via the manager permissions (**canUpdateOffChainBalancesMetadata**), and if the URL is hosted via a permanent file storage solution like IPFS, then the balances will be permanently frozen (never change) but always verifiable.&#x20;

This is because the IPFS URIs are hash-based. So if the hash is permanently stored and frozen on-chain and the balances file is known, you can always verify the hash.

```
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

**How are balances refreshed on the BitBadges indexer for unfrozen off-chain collections?**

First, the URI must be set to return the updated balances. Then, balances can be refreshed on the BitBadges indexer by triggering the refresh API endpoint.

#### Custom Logic Implementation

Off-chain balances' updatable nature allows for the implementation of custom logic for what is returned by the URL. This empowers you to define and program your balance-fetching process to align with your collection's unique requirements.&#x20;

For example, you can dynamically revoke and assign based on if users pay their subscription fees for a month all without ever interacting with the blockchain (since the URL won't change). You simply need to just update the JSON map returned.

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

For another tutorial, see [here](../tutorials/create-an-off-chain-balances-json.md). Or, find a tool or tutorial for your use case on the [Ecosystem ](../../overview/ecosystem.md)page!

### Inherited / Badge-Bound (Coming Soon)

For collections with inherited balances, you can create a new collection and can define details unique to this created collection such as badge metadata, standards, etc.&#x20;

**However, all transfers, approval transactions, and any badge creation transactions will throw an error if attempted.** This is because all of these properties are inherited from the "parent" collection, so they should not be defined in the current collection.

To specify where to inherit from, use the **inheritedBalancesTimeline** field of the collection.


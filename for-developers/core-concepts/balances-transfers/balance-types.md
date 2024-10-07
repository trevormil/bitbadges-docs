# 🪙 Balance Types

BitBadges offers different ways to store the badge balances and owners for your collection as explained [here](../../../overview/how-it-works/balances-types.md) (please read this version first, if not already). The balances type for a collection is determined by the **balancesType** field of the collection interface.

The core collection details are always stored on-chain, but balances can be stored using different methods, each offering their own pros and cons.

<figure><img src="../../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Non-Public

```typescript
"balancesType": "Non-Public"
```

We do give the option to make balances non-public. This can either mean you want to keep balances private through a self-implementation. Or, you may not need balances altogether. If either of these criteria match, you can select this balance type, and we will not display anything about balances on the user interface (everything is left up to you entirely).

## Standard

```json
"balancesType": "Standard"
```

With standard balances, everything is facilitated on-chain in a decentralized manner. All balances are stored on the blockchain, and everything is facilitated through on-chain transfers and approvals. All transfers require sufficient balances and valid approvals for the collection, sender, and recipient.

-   All transfers must specify the collection approvals, sender's outgoing approvals, and recipient's incoming approvals. The collection approvals are managed by the manager and can optionally override the user-level approvals.
-   The "Mint" address has unlimited balances, can only send badges, not receive them. The circulating supply will live out according to the permissions, approvals, and transfers of the collection from the Mint address.
-   Thus, the circulating supply will be restricted by transfers / approvals from the Mint address. You can customize the transferability according to the collection's approvals as explained in [Approvals](transferability-approvals.md) and [Approval Criteria](approval-criteria/).
-   Permissions are also settable to control the updatability of approvals (whether approvals are frozen or editable).

## Off-Chain

With off-chain balances, you will create a new collection on-chain and will define details unique to this created collection such as badge metadata, standards, etc. **However, all transfers and approval transactions will throw an error if attempted because these are to be facilitated off-chain.**

Balances, at any given time, will be queried from the specified URI (stored via the **offChainBalancesMetadata** field of the collection).

With off-chain balances, you sacrifice decentralization and on-chain transfers for scalability, user experience, and more. Off-chain balances lend themselves nicely to many badge use cases because badge allocation is often centralized anyways.

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

-   **Significant Resource Reduction**: The architecture's off-chain nature results in a substantial reduction of resources used by your collection—potentially up to over 99%. This is primarily due to the absence of on-chain transfer transactions and balances. Only the core collection details need to be created / updated on-chain, and future balance updates do not require blockchain transactions (assuming the URI does not change).
-   **Non-Blockchain Data and Tools**: Balances can be customized easier using non-blockchain tools and data. For example, you can give badges to those who have paid subscriptions through a non-blockchain service (Google Pay, etc). Or, you can access your private databases to assign badges dynamically.
-   **No-Cost Updates:** If the balances URL (stored on-chain) remains the same, balances can be updated by simply editing what is returned from the server. This means balances can be updated without interacting with the blockchain and paying transaction fees.
-   **Enhanced User Experience**: Users are relieved from the need to interact directly with the blockchain and incur gas fees. This streamlined user experience enhances accessibility and usability. Badges are automatically populated into a user's portfolio without the user ever executing a blockchain transaction.
-   **Discardability:** Because balances are indexed off-chain, past transfer activity that is no longer relevant and needed can be permanently discarded rather than permanently stored on the blockchain and bloating it.

#### Drawbacks

-   **Scalability vs. Functionality Trade-off**: While off-chain balances offer scalability and user-centric benefits, they entail trade-offs in terms of functionality and decentralization. Mainly,\
    since there are no on-chain transfers, certain on-chain functionality (such as approvals, customizable transferability, transfers w/ $BADGE, and claims) is not supported. Everything is implemented off-chain in a custom manner.
-   **Centralized Trust Factor**: The URL-driven approach introduces a centralized trust element, as the blockchain has no control over the data returned by the URL or the assignment of the balances. This can be mitigated if certain criteria is met (immutable and using permanent storage like IPFS).
-   **Off-Chain Balance Indexing:** Because balance updates are facilitated and indexed off-chain (if at all), there is no on-chain verifiable ledger of transfer transactions. Off-chain indexing does not sacrifice any functionality, but the accuracy and availability may not be on par with on-chain indexing.
    -   Timestamping: There is no decentralized, verifiable log of EXACTLY when each balance update occurs because they occur on a hosted server. Indexers will attempt to fetch and catch each update as fast as possible, if applicable, but there is bound to be delay.
    -   Loss of Historical Data: Logs of past balances may be lost forever if all parties discard / lose the data and can not be reproduced. However, this could also be a good thing as seen in the benefits.

#### Suitability of Off-Chain Balances

We envision collections adopting off-chain balances if they align with one of the following criteria:

1. **Non-Transferable / Soulbound**: If your collection's badges are intrinsically tied to specific users and are not intended for transfer ever, off-chain balances could make your collection scalability without sacrificing verifiability or centralization using permanent, immutable, and decentralized off-chain storage.
2. **Centralized Allocation Control**: In cases where a single entity should maintain complete control over badge allocation (concert tickets, diplomas, etc), the off-chain approach can be particularly beneficial.

#### Advantages Over Standard Solutions

Compared to traditional client-server non-blockchain based solutions, badge collections with off-chain balances offer numerous advantages, including:

-   **Simplified Badge Management**: Outsourcing badge creation, maintenance, and verification reduces your workload.
-   **Seamless Integration**: Integration with the complete suite of BitBadges tools.
-   **Enhanced Security and Availability**: While balances are off-chain, the collection's core creation and foundation remain on the blockchain where it benefits from security, immutability, and availability.
-   **Unified Digital Identity Building**: Users can consolidate their digital identity to their single address, eliminating fragmentation across various websites.

In conclusion, off-chain balances present an intriguing avenue to enhance scalability, user experience, and badge management. While there are considerations and trade-offs, the decision to adopt this approach hinges on your collection's specific goals and priorities. For additional resources and guidance, consult the Ecosystem page to identify suitable tools and tutorials for your use case.

**How are balances permanently frozen?**

The balances URL can be set to non-updatable via the manager permissions (**canUpdateOffChainBalancesMetadata**), and if the URL is hosted via a permanent file storage solution like IPFS, then the balances will be permanently frozen (never change) but always verifiable.

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
    "permanentlyPermittedTimes": [],
    "permanentlyForbiddenTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ]
  }
]
```

#### Custom Logic Implementation

Off-chain balances' updatable nature allows for the implementation of custom logic for what is returned by the URL (if not using a permanent URL). This empowers you to define and program your balance assignment process to align with your collection's unique requirements.

For example, you can dynamically revoke and assign based on if users pay their subscription fees for a month all without ever interacting with the blockchain (since the URL won't change). You simply need to just update the returned balances.

### Indexed vs Non-Indexed (On-Demand)

Off-chain balances can either be indexed or non-indexed. Note that we use non-indexed and on-demand interchangeably.

The differences are as follows:

-   Indexed balances have a total verifiable supply. Non-indexed does not.
-   At any time, for indexed balances, all owners and their balances are known. With non-indexed, this is not tracked, and we fetch on-demand from the source every time.
-   For indexed balances, a ledger of activity is tracked. For non-indexed, there is no ledger kept. You can only view the current balances at any given time.
-   Indexed balances will show up in standard search results like user's portfolios. For non-indexed, you have to check it manually.
-   Indexed balances have a limit of unique owners (set by the indexer) for scalability reasons, whereas non-indexed has no such limit.

Technically speaking, the difference is that non-indexed balances are queried on a per-address basis and only return one balance at a time. Indexed balances return the **entire** list of owners via a JSON map when queried.

### Off-Chain - Indexed Balances

<pre class="language-json"><code class="lang-json"><strong>"balancesType": "Off-Chain - Indexed"
</strong></code></pre>

There are two options when creating off-chain balances: "Off-Chain - Indexed" and "Off-Chain - Non-Indexed".

For indexed balances, indexers are expected to do and know the following:

-   Keep track of ALL owners and their balances at any given time
-   Create a ledger of activity for any balance updates

We do this by the following:

-   There is no enforceable total supply since everything is managed off-chain. The valid badge ID range is still specified on-chain.
-   Each balances query will return a map of ALL balances for the collection. This is done via a JSON of user -> balance definitions. See below.

**What format should the balances be in?**

To facilitate the expected functionality of indexed balances, the returned balances are expected to be in a specific format. It should be a JSON object where the keys are Cosmos addresses / address list IDs and the values are Balance\<string>\[]. See [https://bitbadges-balances.nyc3.digitaloceanspaces.com/airdrop/balances](https://bitbadges-balances.nyc3.digitaloceanspaces.com/airdrop/balances).

Note that if you use address list IDs for the keys ([see here to learn more](../address-lists-lists.md)), the corresponding address list must be a whitelist (whitelist = false) and stored on-chain for reproducability (not off-chain via the BitBadges servers or somewhere else).

See [here](../../bitbadges-sdk/common-snippets/off-chain-balances.md) for further info using the SDK for off-chain balances.

**How are balances refreshed on the BitBadges indexer for unfrozen off-chain collections?**

BitBadges uses a refresh queue to update balances. A refresh can be triggered via the site or the API. If the on-chain URL changes, we auto-trigger a refresh,

#### Custom Logic Implementation

Example:

```typescript
app.get('/api/v0/airdrop/balances', async (req, res) => {
    const allAirdropped = await AIRDROP_DB.list();
    const airdropped = allAirdropped.rows.map((row) => row.id);
    const balancesMap: OffChainBalancesMap<bigint> = {};
    for (const address of airdropped) {
        balancesMap[address] = [
            {
                amount: 1n,
                badgeIds: [
                    {
                        start: 1n,
                        end: 1n,
                    },
                ],
                ownershipTimes: [
                    {
                        start: 1n,
                        end: 18446744073709551615n,
                    },
                ],
            },
        ];
    }
    return res.send(balancesMap);
});
```

This dynamically updates what balances are returned from the URL based on who has received an airdrop or not (using a private airdrop database). This is all done off-chain, meaning balances are updated without a blockchain transaction (i.e. the on-chain URL stays the same as API_URL/api/v0/airdrop/balances)/

### Off-Chain - Non-Indexed (On-Demand)

```json
"balancesType": "Off-Chain - Non-Indexed"
```

Non-indexed balances follow a similar approach to indexed balances, but there is no verifiable total supply defined on-chain. Balances are not indexed, meaning they are expected to be dynamically fetched on-demand, and there is no activity ledger recorded because of this.

At any given time, we do not know the entire list of owners and balances, unlike with indexed balances. As a result, we do not show these balances in search results like a user's portfolio. The only way to query balances for a user is to manually query it.

The benefit of this is that this approach is much more scalable to indexers. Indexers do not need to worry about caching and maintaining logs of balances. As a result, there is no limit on the number of unique owners, whereas there is a limit (currently 15000 for the BitBadges API / Indexer) for standard indexed balances.

All URIs must contain the "{address}" placeholder in the on-chain specified URI. This is to be replaced at fetch time with the user's Cosmos address. it is up to you whether you want to support native chain addresses as well, but the converted Cosmos address is a minimum requirement.

The return value from the URI should be dynamic in the following format:

```
{ balances: [....] }
```

Standard indexed balances return the whole map of user -> balances whereas non-indexed only returns the requested user's balances.

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

The badges range on-chain (the ones via **badgeIdsToAdd**) is only used for the expected badge IDs of the collection.

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

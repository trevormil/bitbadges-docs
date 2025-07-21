# Balances Types

BitBadges offers different ways to store the badge balances and owners for your collection, each with their own pros and cons.

```
NOTE: Off-chain badge balances have now been deprecated on the frontend.

We made this decision because we felt they added too much complexity and could be implemented much simpler with address lists and claims.

If you are interested in us reenabling this, please let us know.
```

<figure><img src="../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

## Standard (On-Chain)

Standard balances are what you may be familiar with. All balances are stored on the blockchain, and users can transfer badges to each other by transacting with the blockchain. The total supply is controlled by on-chain transfers, approvals, and permissions.

This is the least scalable because it uses the most blockchain resources, but it is the most decentralized. Everything is facilitated on the blockchain and can access blockchain data with no trust involved (including $BADGE).

## Non-Public

We do give the option to make balances non-public. This can either mean you want to keep balances private through a self-implementation. Or, you may not need balances altogether. If either of these criteria match, you can select this balance type, and we will not display anything about balances on the user interface (everything is left up to you entirely).

## Off-Chain

Unlike conventional on-chain balances, off-chain balances leverage an alternative approach to balance storage. Core collection details are still stored on the blockchain, but balances are stored off-chain, on a typical server or through a file storage solution like IPFS. The current balance allocations are dynamically fetched from the URL designated for storage (URL is stored on-chain).

Transfers and approvals are not handled by the blockchain. Transfers may be implemented off-chain. This enables access to non blockchain native data, but it also means that blockchain data and logic can not be executed in a trustless manner (e.g. sending $BADGE along with an approval). Everything must occur off-chain.

#### Configurable URL and Permanent Storage

The URL fetching mechanism is customizable. URLs are stored on-chain and can be set to be updatable or non-updatable by the manager. The balances returned by the URL can also be configured be updatable or rely on permanent storage like IPFS, ensuring consistent and unchanging balance data. When URLs are non-updatable and use permanent storage, balances become immutable (aka soulbound or non-transferable), providing long-term stability.

#### Benefits

* **Significant Resource Reduction**: The architecture's off-chain nature results in a substantial reduction of resources used by your collection—potentially up to over 99%. This is primarily due to the absence of on-chain transfer transactions and balances. Only the collection needs to be created / updated on-chain, and future balance updates do not require blockchain transactions.
* **Non-Blockchain Data and Tools**: Balances can be customized using non-blockchain tools and data. While on-chain balances are restricted to on-chain data (smart contracts, etc.), off-chain balances can be customized with other data. For example, you can give badges to those who have paid subscriptions through a non-blockchain service (Google Pay, etc).
* **No-Cost Updates:** If the balances URL (stored on-chain) remains the same, balances can be updated by simply editing what is returned from the server. This means balances can be updated without interacting with the blockchain and paying transaction fees.
* **Enhanced User Experience**: Users are relieved from the need to interact directly with the blockchain and incur gas fees. This streamlined user experience enhances accessibility and usability. Badges are automatically populated into a user's portfolio without the user ever executing a blockchain transaction.
* **Discardability:** Because balances are indexed off-chain, past transfer activity that is no longer relevant and needed can be permanently discarded rather than permanently stored on the blockchain and bloating it.

#### Drawbacks

* **Scalability vs. Functionality Trade-off**: While off-chain balances offer scalability and user-centric benefits, they entail trade-offs in terms of functionality and decentralization. Mainly,\
  since there are no on-chain transfers, certain on-chain functionality (such as approvals, customizable transferability, transfers w/ $BADGE) is not supported. Everything is implemented off-chain in a custom manner.
* **Centralized Trust Factor**: The URL-driven approach introduces a centralized trust element, as the blockchain has no control over the data returned by the URL or the assignment of the balances. This can be mitigated if certain criteria is met (immutable and using permanent storage like IPFS).
* **Off-Chain Balance Indexing:** Because balance updates are facilitated and indexed off-chain, there is no on-chain verifiable ledger of transfer transactions. Off-chain indexing does not sacrifice any functionality, but the accuracy and availability may not be on par with on-chain indexing.
  * Timestamping: There is no decentralized, verifiable log of EXACTLY when each balance update occurs because they occur on a hosted server. Indexers will attempt to fetch and catch each update as fast as possible, if applicable, but there is bound to be delay.
  * Loss of Historical Data: Logs of past balances may be lost forever if all parties discard / lose the data and can not be reproduced. However, this could also be a good thing as seen in the benefits.

### Indexed vs Non-Indexed

Off-chain balances can either be indexed or non-indexed. Note we use on-demand and non-indexed interchangeably. The differences are as follows:

* Indexed balances have a total verifiable supply. Non-indexed does not.
* At any time, for indexed balances, all owners and their balances are known. With non-indexed, this is not tracked, and we fetch on-demand from the source every time.
* For indexed balances, a ledger of activity is tracked. For non-indexed, there is no ledger kept. You can only view the current balances at any given time.
* Indexed balances have a limit of unique owners (set by the indexer) for scalability reasons, whereas non-indexed has no such limit.

### Suitability of Off-Chain Balances

Consider adopting off-chain balances if your collection aligns with the following criteria:

1. **Non-Transferable / Soulbound**: If your collection's badges are intrinsically tied to specific users and are not intended for transfer, off-chain balances could be advantageous, especially if you make the balances frozen and immutable.
2. **Centralized Allocation Control**: In cases where a single entity should maintain complete control over badge allocation (concert tickets, diplomas, etc), the off-chain approach can be particularly beneficial.

This is because there is little added benefit to using a blockchain if such criteria is met. Sure, the blockchain might have better availability and verifiability than an off-chain solution (although, we have been using off-chain solutions for 20+ years successfully). However, other than that, the blockchain really provides little benefit, and fees can be expensive.

### Customizability and Advantages Over Standard Solutions

#### Custom Logic Implementation

Balances' updatable nature allows for the implementation of custom logic for what is returned by the URL. This empowers you to define and program your balance-fetching process to align with your collection's unique requirements. It also allows you to access and integrated with non-blockchain data and tools to customize your balances further.

For example, you can dynamically revoke and assign based on if users pay their subscription fees for a month all without ever interacting with the blockchain (since the URL won't change).

See [here](time-dependent-ownership.md). Or, find a tool or tutorial for your use case on the [Ecosystem](../../ecosystem/) page!

#### Advantages Over Standard Solutions

Compared to traditional client-server solutions, off-chain balances offer numerous advantages, including:

* **Simplified Badge Management**: Outsourcing badge creation, maintenance, and verification reduces your workload.
* **Seamless Integration**: Integration with the complete suite of BitBadges tools.
* **Enhanced Security and Availability**: While balances are off-chain, the collection's core creation and foundation remain on the blockchain where it benefits from security, immutability, and availability.
* **Unified Digital Identity Building**: Users can consolidate their digital identity to their single address, eliminating fragmentation across various websites.

In conclusion, off-chain balances present an intriguing avenue to enhance scalability, user experience, and badge management. While there are considerations and trade-offs, the decision to adopt this approach hinges on your collection's specific goals and priorities. For additional resources and guidance, consult the Ecosystem page to identify suitable tools and tutorials for your use case.

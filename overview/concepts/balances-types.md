# Balances Types

BitBadges offers three different ways to store the badge balances and owners for your collection, each with their own pros and cons.

## Standard

Standard balances are what you may be familiar with. All balances are stored on the blockchain, and users can transfer badges to each other by transacting with the blockchain. This is the most customizable option but also the least scalable because it uses the most blockchain resources.

## Off-Chain

Unlike conventional on-chain balances, off-chain balances leverage an alternative approach to balance storage. Instead, balances are stored off-chain, on a typical server or through a file storage solution like IPFS. The current balance allocations are dynamically fetched from the URL designated for storage (URL is stored on-chain). Transfers and approvals on the blockchain are **not supported.**

#### Configurable URL and Permanent Storage

The URL fetching mechanism is customizable. URLs are stored on-chain and can be set to be updatable or non-updatable by the manager. The balances returned by the URL can also be configured be updatable or rely on permanent storage like IPFS, ensuring consistent and unchanging balance data. When URLs are non-updatable and use permanent storage, balances become immutable (aka soulbound or non-transferable), providing long-term stability.

#### Benefits

* **Significant Resource Reduction**: The architecture's off-chain nature results in a substantial reduction of resources used by your collection—potentially up to over 99%. This is primarily due to the absence of on-chain transfer transactions and balances.
* **No-Cost Updates:** If the balances URL (stored on-chain) remains the same, balances can be updated by simply editing what is returned from the server. This means balances can be updated without interacting with the blockchain and paying transaction fees.
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

### Customizability and Advantages Over Standard Solutions

#### Custom Logic Implementation

Balances' updatable nature allows for the implementation of custom logic for what is returned by the URL. This empowers you to define and program your balance-fetching process to align with your collection's unique requirements.&#x20;

For example, you can dynamically revoke and assign based on if users pay their subscription fees for a month all without ever interacting with the blockchain (since the URL won't change).

See [here](../../for-developers/tutorials/create-an-off-chain-balances-json.md). Or, find a tool or tutorial for your use case on the [Ecosystem ](../ecosystem.md)page!

#### Advantages Over Standard Solutions

Compared to traditional client-server solutions, off-chain balances offer numerous advantages, including:

* **Simplified Badge Management**: Outsourcing badge creation, maintenance, and verification reduces your workload.
* **Seamless Integration**: Integration with the complete suite of BitBadges tools.
* **Enhanced Security and Availability**: While balances are off-chain, the collection's core creation and foundation remain on the blockchain where it benefits from security, immutability, and availability.
* **Unified Digital Identity Building**: Users can consolidate their digital identity to their single address, eliminating fragmentation across various websites.

In conclusion, off-chain balances present an intriguing avenue to enhance scalability, user experience, and badge management. While there are considerations and trade-offs, the decision to adopt this approach hinges on your collection's specific goals and priorities. For additional resources and guidance, consult the Ecosystem page to identify suitable tools and tutorials for your use case.

## Inherited / Badge-Bound (Coming Soon)

Inherited (or badge-bound) balances are a way for a collection to inherit the owners of badges from another collection. For example, I want to send a free coupon badge which automatically distributes to all owners of my membership badge.&#x20;

If the "parent" badge transfers owners, all child badges will automatically transfer owners as well.






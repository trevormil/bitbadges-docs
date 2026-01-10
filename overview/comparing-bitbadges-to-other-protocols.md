# ⚖️ BitBadges L1 vs Others

## Comparing BitBadges L1 to Other Protocols

BitBadges is a unique L1 (Layer 1) blockchain built using the Cosmos SDK. The BitBadges token standard is not EVM-compatible, ERC-20 compatible, or Bitcoin Ordinals compatible. Instead, we offer our own flexible, ever-evolving token standard built on Cosmos foundations.

While BitBadges may lack some of the native smart contract support and interoperability features of other protocols, its self-contained design and API-like token standard can be a compelling solution for certain use cases.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Chain Architecture

BitBadges is its own L1 blockchain, not a layer-2 or sidechain solution. It is built using the Cosmos SDK, which gives it flexibility and scalability. It also connects to the IBC ecosystem and all other Cosmos features.

### Security Model

Yes, BitBadges may not be as secure or decentralized (yet) as some other protocols. It is its own L1, and over time, we will only get more decentralized and secure. However, we actually envision BitBadges being a flexible part of any application stack and used where needed rather than an all encompassing solution.

For example, when building an application, you may use BitBadges for authentication / website gating but still accept payments in your native or preferred currency.

### Cross-Chain Interoperability

BitBadges leverages IBC to connect to other chains and use the BitBadges token standard on any Cosmos chain.

### Token Standard

BitBadges offers its own token standard built from the ground up that functions more like an API where everything is already implemented natively, and you just customize the requests (as Cosmos SDK Msgs) behind the scenes. This is how the BitBadges site is all no-code by default.

This approach vastly differs to existing ones requiring an individual smart contract for all tokens. Protocols like Ethereum rely on ERC-20 tokens, which require individual smart contract deployments and management. This approach gets complex, vulnerable to security flaws, and does not support the required structure.

### Smart Contract Support

While BitBadges does not natively support EVM or ERC-20 contracts, it does support CosmWASM contracts, allowing for the extension of functionality and the creation of dApps. However, our goal is to keep evolving our token standard so that no custom contracts are EVER needed.

Protocols like Ethereum and Solana have robust smart contract support, enabling a wide range of decentralized applications.

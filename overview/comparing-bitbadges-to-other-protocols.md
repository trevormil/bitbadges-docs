# ⚖️ Comparing BitBadges to Other Protocols

## Comparing BitBadges to Other Protocols

BitBadges is a unique L1 blockchain built using the Cosmos SDK that aims to simplify the multi-chain experience. BitBadges is not EVM-compatible, ERC-20 compatible, or Bitcoin Ordinals compatible. Instead, it offers its own flexible, ever-evolving token standard that functions more like an API, making it easier to build multi-chain applications. This is because it is compatible with any wallet from any chain.

While BitBadges may lack some of the native smart contract support and interoperability features of other protocols, its self-contained design and API-like token standard can be a compelling solution for certain use cases.

### Chain Architecture

BitBadges is its own L1 blockchain, not a layer-2 or sidechain solution. It is built using the Cosmos SDK, which gives it flexibility and scalability. It also connects to the IBC ecosystem.

Protocols like Bitcoin Oracles, Ethereum NFTs, and Solana NFTs are all deployed on their respective chain and not compatible with each other.  If you wanted to implement a token-gated application for all those chains, you would need to issue tokens on every chain. With BitBadges, it is one token in one place for all users from any chain.

### Cross-Chain Interoperability

BitBadges is not interoperable in the traditional sense, as it is a self-contained L1 blockchain. However, it supports users and wallets from any chain, allowing for easy multi-chain token transfers and multi-chain application development (e.g. Ethereum users can transfer badges to Solana users to Bitcoin users). For example, Bitcoin users can sign BitBadges transactions with their Bitcoin wallets.

BitBadges does not "pull" or "connect" data from other chains. All relevant data is stored on the BitBadges chain, simplifying application development.

### Token Standard

BitBadges offers its own token standard built from the ground up that functions more like an API where everything is already implemented natively, and you just customize the requests (as Cosmos SDK Msgs) behind the scenes. This is how the BitBadges site is all no-code by default.&#x20;

This approach vastly differs to existing ones requiring an individual smart contract for all tokens. Protocols like Ethereum rely on ERC-20 tokens, which require individual smart contract deployments and management. This approach gets complex, vulnerable to security flaws, and does not support the required structure.

### Smart Contract Support

While BitBadges does not natively support EVM or ERC-20 contracts, it does support CosmWASM contracts, allowing for the extension of functionality and the creation of dApps. However, our goal is to keep evolving our token standard so that no custom contracts are EVER needed.

Protocols like Ethereum and Solana have robust smart contract support, enabling a wide range of decentralized applications.

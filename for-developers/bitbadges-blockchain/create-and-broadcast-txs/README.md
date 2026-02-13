# Create, Generate, and Sign Txs

There are multiple ways you can create, generate, and sign transactions.&#x20;

-   CLI: Directly interact with the blockchain node's CLI in a terminal
-   Directly via Cosmos SDK practices like Keplr signing
-   Via our SDK

We recommend generating, signing, and broadcasting your transactions with the [BitBadges SDK](../../bitbadges-sdk/). The SDK provides easy-to-use TypeScript functions to construct transactions of all types and broadcast them to a blockchain node.

## Signing Methods

BitBadges Chain supports two signing methods:

- **[Signing - Cosmos](./signing-cosmos.md)**: Sign transactions using Cosmos wallets (Keplr, Leap, etc.)
- **[Signing - Ethereum](./signing-ethereum.md)**: Sign transactions using Ethereum wallets (MetaMask, Privy, etc.) via EVM precompiles

The SDK automatically handles message conversion to EVM precompile calls when an EVM address is provided in the transaction context.

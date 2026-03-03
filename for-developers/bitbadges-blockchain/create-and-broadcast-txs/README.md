# Create, Generate, and Sign Txs

There are multiple ways you can create, generate, and sign transactions.&#x20;

-   CLI: Directly interact with the blockchain node's CLI in a terminal
-   Directly via Cosmos SDK practices like Keplr signing
-   Via our SDK

We recommend generating, signing, and broadcasting your transactions with the [BitBadges SDK](../../bitbadges-sdk/). The SDK provides easy-to-use TypeScript functions to construct transactions of all types and broadcast them to a blockchain node.

## Two Approaches

The SDK offers two approaches for signing transactions:

### 1. BitBadgesSigningClient (Streamlined)

The **[BitBadgesSigningClient](./signing-client.md)** provides a simple, all-in-one solution that handles everything automatically:
- Account info fetching and caching
- Gas estimation via simulation
- Signing with any wallet (Cosmos or EVM)
- Broadcasting to the network
- Automatic sequence retry on mismatch

```typescript
import { BitBadgesSigningClient, GenericCosmosAdapter } from 'bitbadgesjs-sdk';

const adapter = await GenericCosmosAdapter.fromKeplr('bitbadges-1');
const client = new BitBadgesSigningClient({ adapter });

const result = await client.signAndBroadcast([msg]);
console.log('TX Hash:', result.txHash);
```

**Best for:** Quick integration, simple use cases, when you don't need fine-grained control.

### 2. Manual Signing (Fine-Grained Control)

For more control over the transaction lifecycle, use the manual signing approach:
- **[Signing - Cosmos](./signing-cosmos.md)**: Sign with Keplr, Leap, Cosmostation, etc.
- **[Signing - Ethereum](./signing-ethereum.md)**: Sign with MetaMask, Privy, etc. via EVM precompiles

This approach gives you full control over:
- Transaction context construction
- Fee and gas configuration
- Simulation and gas estimation
- Signing flow customization
- Broadcasting timing and retry logic

**Best for:** Custom integrations, complex transaction flows, when you need full control.

## Comparison

| Feature | BitBadgesSigningClient | Manual Signing |
|---------|----------------------|----------------|
| Setup | Minimal | More code required |
| Control | Abstracted | Full control |
| Account Info | Auto-fetched & cached | Manual fetch |
| Gas Estimation | Built-in | Manual simulation |
| Sequence Retry | Automatic | Manual handling |
| Custom Flows | Limited | Fully customizable |

## Wallet Support

Both approaches support:

| Wallet Type | Examples |
|-------------|----------|
| Cosmos | Keplr, Leap, Cosmostation |
| EVM | MetaMask, Privy, any EIP-1193 provider |
| Server-side | Mnemonic, Private Key |

The SDK automatically handles message conversion to EVM precompile calls when using an EVM wallet.

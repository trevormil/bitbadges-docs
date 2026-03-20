# Create, Generate, and Sign Txs

There are multiple ways you can create, generate, and sign transactions.&#x20;

-   **CLI**: Use the [`bitbadgeschaind` CLI](./chain-cli.md) to manage keys and submit transactions directly from a terminal -- no code required
-   **SDK (Recommended)**: Use the [BitBadges SDK](../../bitbadges-sdk/) for TypeScript-based signing and broadcasting
-   **Manual Signing**: Directly via Cosmos SDK practices like Keplr or MetaMask signing for fine-grained control

## Three Approaches

### 1. Chain CLI (No Code)

The **[`bitbadgeschaind` CLI](./chain-cli.md)** lets you manage keys and submit transactions directly from a terminal. No TypeScript or SDK required.

```bash
# Create a key
bitbadgeschaind keys add mykey

# Create a collection
bitbadgeschaind tx tokenization create-collection ./collection.json \
  --from mykey --chain-id bitbadges-1 --node https://lcd.bitbadges.io:443
```

**Best for:** Quick testing, server-side automation, scripting, agent wallets, environments without Node.js.

### 2. BitBadgesSigningClient (Streamlined)

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

### 3. Manual Signing (Fine-Grained Control)

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

| Feature | Chain CLI | BitBadgesSigningClient | Manual Signing |
|---------|-----------|----------------------|----------------|
| Language | None (terminal) | TypeScript | TypeScript |
| Setup | Build binary | Minimal | More code required |
| Control | Flag-based | Abstracted | Full control |
| Account Info | Automatic | Auto-fetched & cached | Manual fetch |
| Gas Estimation | `--gas auto` | Built-in | Manual simulation |
| Sequence Retry | N/A | Automatic | Manual handling |
| Custom Flows | Shell scripts | Limited | Fully customizable |

## Wallet Support

Both approaches support:

| Wallet Type | Examples |
|-------------|----------|
| Cosmos | Keplr, Leap, Cosmostation |
| EVM | MetaMask, Privy, any EIP-1193 provider |
| Server-side | Mnemonic, Private Key |

The SDK automatically handles message conversion to EVM precompile calls when using an EVM wallet.

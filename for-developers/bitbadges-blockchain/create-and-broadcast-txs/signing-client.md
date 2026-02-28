# BitBadgesSigningClient (Streamlined)

The `BitBadgesSigningClient` provides a streamlined, wallet-agnostic interface for signing and broadcasting transactions. It handles account info fetching, gas estimation, signing, and broadcasting in a single call.

## When to Use

**Use BitBadgesSigningClient when:**
- You want a simple, all-in-one signing solution
- You don't need fine-grained control over the signing process
- You want automatic gas estimation and sequence management
- You want automatic retry on sequence mismatch errors

**Use manual signing ([Cosmos](./signing-cosmos.md) / [Ethereum](./signing-ethereum.md)) when:**
- You need full control over the transaction lifecycle
- You need to customize the signing flow (e.g., custom fee logic)
- You need to handle simulation separately from signing
- You're integrating with existing transaction management infrastructure

## Installation

The signing client is included in the main SDK:

```bash
npm install bitbadgesjs-sdk
```

## Quick Start

### Cosmos Wallet (Keplr, Leap, etc.)

```typescript
import { BitBadgesSigningClient, GenericCosmosAdapter, MsgTransferTokens } from 'bitbadgesjs-sdk';

// Create adapter from browser wallet
const adapter = await GenericCosmosAdapter.fromKeplr('bitbadges-1');
// Or: GenericCosmosAdapter.fromLeap('bitbadges-1')
// Or: GenericCosmosAdapter.fromCosmostation('bitbadges-1')

// Create signing client
const client = new BitBadgesSigningClient({ adapter });

// Create and broadcast transaction
const result = await client.signAndBroadcast([
  MsgTransferTokens.create({
    creator: client.address,
    collectionId: '1',
    transfers: [/* ... */]
  })
]);

if (result.success) {
  console.log('Transaction hash:', result.txHash);
} else {
  console.error('Transaction failed:', result.error);
}
```

### EVM Wallet (MetaMask, etc.)

```typescript
import { BitBadgesSigningClient, GenericEvmAdapter, MsgTransferTokens } from 'bitbadgesjs-sdk';
import { BrowserProvider } from 'ethers';

// Create adapter from ethers.js signer
const provider = new BrowserProvider(window.ethereum);
const signer = await provider.getSigner();
const adapter = await GenericEvmAdapter.fromSigner(signer);

// Or directly from window.ethereum
// const adapter = await GenericEvmAdapter.fromBrowserWallet();

// Create signing client
const client = new BitBadgesSigningClient({ adapter });

// Create and broadcast transaction (uses precompile path automatically)
const result = await client.signAndBroadcast([
  MsgTransferTokens.create({
    creator: client.address,
    collectionId: '1',
    transfers: [/* ... */]
  })
]);

console.log('Transaction hash:', result.txHash);
```

### Server-Side Signing

```typescript
import { BitBadgesSigningClient, GenericCosmosAdapter } from 'bitbadgesjs-sdk';

// From mnemonic (secure server-side only!)
const adapter = await GenericCosmosAdapter.fromMnemonic(
  'word1 word2 word3 ...', // 12 or 24 word mnemonic
  'bitbadges-1'
);

// Or from private key
const adapter = await GenericCosmosAdapter.fromPrivateKey(
  '0x...', // hex private key
  'bitbadges-1'
);

const client = new BitBadgesSigningClient({ adapter });
const result = await client.signAndBroadcast([/* messages */]);
```

## Client Options

```typescript
interface SigningClientOptions {
  adapter: WalletAdapter;              // Required - the wallet adapter
  network?: NetworkMode;               // 'mainnet' | 'testnet' | 'local' (default: 'mainnet')
  apiUrl?: string;                     // Override API URL from network preset
  nodeUrl?: string;                    // Override node LCD URL from network preset
  cosmosChainId?: string;              // Override Cosmos chain ID from network preset
  evmChainId?: number;                 // Override EVM chain ID from network preset
  sequenceRetryEnabled?: boolean;      // Auto-retry on sequence mismatch (default: true)
  maxSequenceRetries?: number;         // Max retry attempts (default: 3)
  gasMultiplier?: number;              // Gas estimation multiplier (default: 1.3)
  defaultGasLimit?: number;            // Default gas limit (default: 400000)
  evmPrecompileGasLimit?: number;      // EVM precompile gas limit (default: 2000000)
  apiKey?: string;                     // BitBadges API key for authenticated requests
}
```

## Network Presets

The SDK provides built-in network configurations via `NETWORK_CONFIGS`:

```typescript
import { NETWORK_CONFIGS, type NetworkMode } from 'bitbadgesjs-sdk';

// Available presets
const mainnet = NETWORK_CONFIGS['mainnet'];
// {
//   apiUrl: 'https://api.bitbadges.io',
//   nodeUrl: 'https://lcd.bitbadges.io',
//   cosmosChainId: 'bitbadges-1',
//   evmChainId: 50024
// }

const testnet = NETWORK_CONFIGS['testnet'];
// {
//   apiUrl: 'https://api.bitbadges.io/testnet',
//   nodeUrl: 'https://lcd-testnet.bitbadges.io',
//   cosmosChainId: 'bitbadges-2',
//   evmChainId: 50025
// }

const local = NETWORK_CONFIGS['local'];
// {
//   apiUrl: 'http://localhost:3001',
//   nodeUrl: 'http://localhost:1317',
//   cosmosChainId: 'bitbadges-1',
//   evmChainId: 90123
// }
```

Use network presets with optional overrides:

```typescript
// Use mainnet preset (default)
const client = new BitBadgesSigningClient({ adapter });

// Use testnet preset
const client = new BitBadgesSigningClient({
  adapter,
  network: 'testnet'
});

// Use preset with custom override
const client = new BitBadgesSigningClient({
  adapter,
  network: 'mainnet',
  apiUrl: 'https://custom-api.example.com'  // overrides preset's apiUrl
});
```

## Sign and Broadcast Options

```typescript
interface SignAndBroadcastOptions {
  memo?: string;           // Transaction memo
  fee?: SigningFee;        // Custom fee (overrides auto-calculation)
  simulate?: boolean;      // Simulate first for gas estimation (default: true for Cosmos)
  gasMultiplier?: number;  // Override gas multiplier for this transaction
}
```

## Broadcast Result

```typescript
interface BroadcastResult {
  txHash: string;       // Transaction hash
  success: boolean;     // Whether broadcast succeeded
  error?: string;       // Error message if failed
  code: number;         // Transaction code (0 = success)
  rawResponse: any;     // Raw response from broadcast endpoint
}
```

## Wallet Adapters

### GenericCosmosAdapter

Factory methods for Cosmos wallets:

```typescript
// Browser wallets
GenericCosmosAdapter.fromKeplr(chainId: string)
GenericCosmosAdapter.fromLeap(chainId: string)
GenericCosmosAdapter.fromCosmostation(chainId: string)
GenericCosmosAdapter.fromBrowserWallet(wallet: KeplrLike, chainId: string)

// Server-side
GenericCosmosAdapter.fromMnemonic(mnemonic: string, chainId: string)
GenericCosmosAdapter.fromPrivateKey(privateKey: string, chainId: string)
```

### GenericEvmAdapter

Factory methods for EVM wallets:

```typescript
// From ethers.js signer
GenericEvmAdapter.fromSigner(signer: EthersSigner, options?: EvmAdapterOptions)

// From EIP-1193 provider
GenericEvmAdapter.fromProvider(provider: EIP1193Provider, options?: EvmAdapterOptions)

// From window.ethereum
GenericEvmAdapter.fromBrowserWallet(options?: EvmAdapterOptions)
```

#### EVM Chain ID Validation

You can validate that the wallet is connected to the correct EVM network:

```typescript
import { GenericEvmAdapter, NETWORK_CONFIGS } from 'bitbadgesjs-sdk';

// Validate chain ID when creating adapter
const adapter = await GenericEvmAdapter.fromSigner(signer, {
  expectedChainId: NETWORK_CONFIGS['mainnet'].evmChainId  // 50024
});

// Or for local development
const adapter = await GenericEvmAdapter.fromBrowserWallet({
  expectedChainId: NETWORK_CONFIGS['local'].evmChainId  // 90123
});

// Throws error if wallet is on wrong network:
// "Wallet is connected to chain X, but expected chain Y. Please switch your wallet to the correct network."
```

## Network Selection

```typescript
// Mainnet (default)
const client = new BitBadgesSigningClient({
  adapter,
  network: 'mainnet'
});

// Testnet
const client = new BitBadgesSigningClient({
  adapter,
  network: 'testnet'
});

// Local development
const client = new BitBadgesSigningClient({
  adapter,
  network: 'local'
});
```

## Gas Estimation

By default, the client simulates transactions to estimate gas:

```typescript
// Auto gas estimation (default)
const result = await client.signAndBroadcast(messages);

// Skip simulation, use default gas
const result = await client.signAndBroadcast(messages, {
  simulate: false
});

// Custom fee
const result = await client.signAndBroadcast(messages, {
  fee: {
    amount: '10000000',
    denom: 'ubadge',
    gas: '500000'
  }
});
```

## Sequence Management

The client automatically handles account sequence (nonce) management:

```typescript
const client = new BitBadgesSigningClient({
  adapter,
  sequenceRetryEnabled: true,  // Auto-retry on sequence mismatch (default)
  maxSequenceRetries: 3        // Max retries (default)
});

// Cached sequence is incremented after successful transactions
// If sequence mismatch occurs, client will:
// 1. Clear cached account info
// 2. Fetch fresh sequence from chain
// 3. Retry the transaction
```

## Account Info

```typescript
// Get account info (cached)
const info = await client.getAccountInfo();
console.log(info.address, info.accountNumber, info.sequence);

// Force refresh from chain
const freshInfo = await client.getAccountInfo(true);

// Clear cache manually
client.clearCache();
```

## Multiple Messages

```typescript
const result = await client.signAndBroadcast([
  MsgTransferTokens.create({ /* ... */ }),
  MsgSetTokenMetadata.create({ /* ... */ }),
  MsgSetCollectionMetadata.create({ /* ... */ })
], {
  memo: 'Batch update'
});
```

## Error Handling

```typescript
try {
  const result = await client.signAndBroadcast(messages);

  if (!result.success) {
    // Transaction was broadcast but failed on-chain
    console.error('Transaction failed:', result.error);
    console.error('Error code:', result.code);
  }
} catch (error) {
  // Pre-broadcast error (wallet rejected, network error, etc.)
  console.error('Signing error:', error.message);
}
```

## Comparison with Manual Signing

| Feature | BitBadgesSigningClient | Manual Signing |
|---------|----------------------|----------------|
| Complexity | Simple, all-in-one | More setup required |
| Control | Abstracted | Full control |
| Account Info | Auto-fetched & cached | Manual fetch |
| Gas Estimation | Built-in simulation | Manual simulation |
| Sequence Retry | Automatic | Manual handling |
| Broadcasting | Built-in | Separate step |
| Custom Flow | Limited | Fully customizable |

## See Also

- [Signing - Cosmos](./signing-cosmos.md) - Manual Cosmos wallet signing
- [Signing - Ethereum](./signing-ethereum.md) - Manual EVM wallet signing
- [Transaction Context](./transaction-context.md) - Understanding TxContext
- [Broadcast to Node](./broadcast-to-a-node.md) - Manual broadcasting

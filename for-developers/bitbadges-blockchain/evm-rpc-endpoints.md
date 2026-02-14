# EVM RPC Endpoints

BitBadges Chain provides Ethereum-compatible JSON-RPC endpoints for interacting with the blockchain using standard Ethereum tools and libraries.

## Overview

The EVM RPC endpoints support all standard Ethereum JSON-RPC methods, allowing you to use tools like:
- MetaMask
- ethers.js
- web3.js
- Hardhat
- Foundry
- Any other Ethereum-compatible tooling

## Mainnet Endpoints

### EVM JSON-RPC

**Endpoint**: `https://evm-rpc.bitbadges.io`

This endpoint provides full Ethereum JSON-RPC compatibility for mainnet operations.

**Chain ID**: `50024`

**Example Usage**:

```typescript
import { ethers } from "ethers";

// Connect to BitBadges mainnet EVM RPC
const provider = new ethers.JsonRpcProvider("https://evm-rpc.bitbadges.io");

// Get the current block number
const blockNumber = await provider.getBlockNumber();
console.log("Current block:", blockNumber);

// Get balance of an address
const balance = await provider.getBalance("0x...");
console.log("Balance:", ethers.formatEther(balance), "BADGE");
```

```javascript
// Using web3.js
const Web3 = require('web3');
const web3 = new Web3('https://evm-rpc.bitbadges.io');

// Get the current block number
const blockNumber = await web3.eth.getBlockNumber();
console.log("Current block:", blockNumber);
```

### MetaMask Configuration

To add BitBadges mainnet to MetaMask:

1. Open MetaMask
2. Go to **Settings > Networks > Add Network**
3. Enter the following details:
   - **Network Name**: BitBadges Mainnet
   - **RPC URL**: `https://evm-rpc.bitbadges.io`
   - **Chain ID**: `50024`
   - **Currency Symbol**: `BADGE`
   - **Block Explorer URL**: `https://explorer.bitbadges.io` (optional)

## Testnet Endpoints

### EVM JSON-RPC

**Endpoint**: `https://evm-rpc-testnet.bitbadges.io`

This endpoint provides full Ethereum JSON-RPC compatibility for testnet operations.

**Chain ID**: `50025`

**Example Usage**:

```typescript
import { ethers } from "ethers";

// Connect to BitBadges testnet EVM RPC
const provider = new ethers.JsonRpcProvider("https://evm-rpc-testnet.bitbadges.io");

// Get the current block number
const blockNumber = await provider.getBlockNumber();
console.log("Current block:", blockNumber);
```

### MetaMask Configuration

To add BitBadges testnet to MetaMask:

1. Open MetaMask
2. Go to **Settings > Networks > Add Network**
3. Enter the following details:
   - **Network Name**: BitBadges Testnet
   - **RPC URL**: `https://evm-rpc-testnet.bitbadges.io`
   - **Chain ID**: `50025`
   - **Currency Symbol**: `BADGE`
   - **Block Explorer URL**: (Testnet block explorer URL, if available)

## Supported JSON-RPC Methods

The EVM RPC endpoints support all standard Ethereum JSON-RPC methods, including:

### Account Methods
- `eth_accounts` - Returns a list of addresses owned by client
- `eth_getBalance` - Returns the balance of the account of given address
- `eth_getTransactionCount` - Returns the number of transactions sent from an address

### Block Methods
- `eth_blockNumber` - Returns the number of most recent block
- `eth_getBlockByNumber` - Returns information about a block by block number
- `eth_getBlockByHash` - Returns information about a block by block hash

### Transaction Methods
- `eth_sendTransaction` - Creates new message call transaction or a contract creation
- `eth_sendRawTransaction` - Sends a signed transaction
- `eth_getTransactionByHash` - Returns the information about a transaction requested by transaction hash
- `eth_getTransactionReceipt` - Returns the receipt of a transaction by transaction hash

### Contract Methods
- `eth_call` - Executes a new message call immediately without creating a transaction on the block chain
- `eth_estimateGas` - Generates and returns an estimate of how much gas is necessary to allow the transaction to complete

### Event Methods
- `eth_getLogs` - Returns an array of all logs matching a given filter object
- `eth_newFilter` - Creates a filter object, based on filter options
- `eth_newBlockFilter` - Creates a filter in the node, to notify when a new block arrives
- `eth_newPendingTransactionFilter` - Creates a filter in the node, to notify when new pending transactions arrive

### State Methods
- `eth_getCode` - Returns code at a given address
- `eth_getStorageAt` - Returns the value from a storage position at a given address

### Network Methods
- `eth_chainId` - Returns the current chain ID
- `net_version` - Returns the current network ID
- `net_listening` - Returns true if client is actively listening for network connections

### Web3 Methods
- `web3_clientVersion` - Returns the current client version
- `web3_sha3` - Returns Keccak-256 hash of the given data

## Example: Deploying a Contract

```typescript
import { ethers } from "ethers";
import * as fs from "fs";

async function deploy() {
  // Connect to BitBadges mainnet EVM RPC
  const provider = new ethers.JsonRpcProvider("https://evm-rpc.bitbadges.io");
  
  // Get deployer wallet
  const privateKey = process.env.PRIVATE_KEY || "";
  if (!privateKey) {
    throw new Error("PRIVATE_KEY environment variable required");
  }
  
  const wallet = new ethers.Wallet(privateKey, provider);
  console.log("Deployer address:", wallet.address);
  
  // Check balance
  const balance = await provider.getBalance(wallet.address);
  console.log("Balance:", ethers.formatEther(balance), "BADGE");
  
  // Deploy contract
  const contractFactory = new ethers.ContractFactory(
    CONTRACT_ABI,
    CONTRACT_BYTECODE,
    wallet
  );
  
  const contract = await contractFactory.deploy(/* constructor args */);
  await contract.waitForDeployment();
  
  const address = await contract.getAddress();
  console.log("Contract deployed at:", address);
}
```

## Example: Interacting with Contracts

```typescript
import { ethers } from "ethers";

async function interactWithContract() {
  // Connect to BitBadges mainnet EVM RPC
  const provider = new ethers.JsonRpcProvider("https://evm-rpc.bitbadges.io");
  
  // Load contract
  const contractAddress = "0x..."; // Your contract address
  const contract = new ethers.Contract(
    contractAddress,
    CONTRACT_ABI,
    provider
  );
  
  // Read from contract
  const value = await contract.someViewFunction();
  console.log("Value:", value);
  
  // Write to contract (requires signer)
  const signer = new ethers.Wallet(process.env.PRIVATE_KEY!, provider);
  const contractWithSigner = contract.connect(signer);
  
  const tx = await contractWithSigner.someWriteFunction(/* args */);
  await tx.wait();
  console.log("Transaction confirmed:", tx.hash);
}
```

## Hardhat Configuration

Add BitBadges networks to your `hardhat.config.js`:

```javascript
require("@nomicfoundation/hardhat-toolbox");

module.exports = {
  solidity: "0.8.20",
  networks: {
    bitbadges: {
      url: "https://evm-rpc.bitbadges.io",
      chainId: 50024,
      accounts: process.env.PRIVATE_KEY ? [process.env.PRIVATE_KEY] : [],
    },
    bitbadgesTestnet: {
      url: "https://evm-rpc-testnet.bitbadges.io",
      chainId: 50025,
      accounts: process.env.PRIVATE_KEY ? [process.env.PRIVATE_KEY] : [],
    },
  },
};
```

## Foundry Configuration

Add BitBadges networks to your `foundry.toml`:

```toml
[rpc_endpoints]
bitbadges = "https://evm-rpc.bitbadges.io"
bitbadgesTestnet = "https://evm-rpc-testnet.bitbadges.io"

[profile.default]
rpc_endpoints = ["bitbadges", "bitbadgesTestnet"]
```

## Rate Limiting

The public EVM RPC endpoints may have rate limiting in place to ensure fair usage. For production applications, consider:

- Running your own node
- Using a dedicated RPC provider
- Implementing request caching and batching

## Related Documentation

- [EVM Integration Guide](../evm/EVM_INTEGRATION.md) - Complete EVM integration overview
- [Setup and Configuration](../evm/evm-precompiles/setup-and-configuration.md) - Local development setup
- [Developer Guide](../evm/evm-precompiles/developer-guide.md) - EVM development best practices
- [Tokenization Precompile API](../evm/evm-precompiles/tokenization-precompile/API.md) - Precompile contract reference

## Other Endpoints

For Cosmos SDK operations, use the Tendermint RPC endpoints:

- **Mainnet RPC**: `https://rpc.bitbadges.io`
- **Mainnet REST/LCD**: `https://lcd.bitbadges.io`
- **Testnet RPC**: `https://rpc-testnet.bitbadges.io`
- **Testnet REST/LCD**: `https://lcd-testnet.bitbadges.io`

See the [Blockchain Overview](overview.md) for more information about Cosmos SDK endpoints.



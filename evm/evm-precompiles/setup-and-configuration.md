# Setup and Configuration

This guide covers everything you need to set up your development environment for building dApps on BitBadges Chain with EVM compatibility.

## Chain Configuration

### Chain ID

The BitBadges Chain EVM uses a custom Chain ID for EVM compatibility:

- **Local Development**: `90123`
- **Testnet**: `50025` (claimed in ethereum-lists/chains registry)
- **Mainnet**: `50024` (claimed in ethereum-lists/chains registry)

The Chain ID is configured in `app/params/constants.go`:

```go
// EVMChainIDMainnet is the EVM chain ID for BitBadges mainnet
// Chain ID: 50024 (claimed in ethereum-lists/chains registry)
// This should match the chain_id in genesis under app_state.evm.params.chain_config.chain_id
EVMChainIDMainnet = "50024"

// EVMChainIDTestnet is the EVM chain ID for BitBadges testnet
// Chain ID: 50025 (claimed in ethereum-lists/chains registry)
// This should match the chain_id in genesis under app_state.evm.params.chain_config.chain_id
EVMChainIDTestnet = "50025"

EVMChainID = "90123" // Default for local development/testing
```

**Important**: The Chain ID in your genesis file (`app_state.evm.params.chain_config.chain_id`) must match this value.

### RPC Endpoints

BitBadges Chain provides two RPC interfaces:

1. **Tendermint RPC** (Port 26657)
   - Used for Cosmos SDK queries and transactions
   - Endpoint: `http://localhost:26657`
   - Supports standard Cosmos SDK operations

2. **EVM JSON-RPC** (Port 8545)
   - Used for Ethereum-compatible operations
   - Endpoint: `http://localhost:8545`
   - Supports standard Ethereum JSON-RPC methods
   - Required for MetaMask, ethers.js, web3.js, etc.

### Starting the Chain with EVM

To start the chain with EVM support, use the `--json-rpc.enable` flag:

```bash
# From the bitbadgeschain repository root
bitbadgeschaind start --json-rpc.enable --json-rpc.address 0.0.0.0:8545
```

Or use the provided startup script:

```bash
# From the bitbadgeschain repository root
./start-chain.sh start
```

The startup script automatically enables EVM JSON-RPC on port 8545.

**Note**: The EVM module is always enabled in the chain binary. You only need to enable the JSON-RPC server to interact with it via Ethereum-compatible tools (MetaMask, ethers.js, etc.).

## MetaMask Configuration

To connect MetaMask to your local BitBadges chain:

1. Open MetaMask
2. Go to **Settings > Networks > Add Network**
3. Add the following network configuration:

**For Local Development:**
- **Network Name**: BitBadges Local
- **RPC URL**: `http://localhost:8545` (EVM JSON-RPC)
- **Chain ID**: `90123`
- **Currency Symbol**: `BADGE`
- **Block Explorer**: (Optional, leave blank for local)

**For Testnet:**
- **Network Name**: BitBadges Testnet
- **RPC URL**: `https://evm-rpc-testnet.bitbadges.io`
- **Chain ID**: `50025`
- **Currency Symbol**: `BADGE`
- **Block Explorer**: (Testnet block explorer URL)

**For Mainnet:**
- **Network Name**: BitBadges Mainnet
- **RPC URL**: `https://evm-rpc.bitbadges.io`
- **Chain ID**: `50024`
- **Currency Symbol**: `BADGE`
- **Block Explorer**: `https://explorer.bitbadges.io`

**Note**: Use port 8545 (EVM JSON-RPC), not 26657 (Tendermint RPC).

### Getting Test Tokens

After starting your local chain, fund your MetaMask account:

```bash
# Get your MetaMask address (0x... format)
# Then fund it using the chain's genesis or by transferring from a validator

# Option 1: Transfer from a validator account
bitbadgeschaind tx bank send \
  $(bitbadgeschaind keys show alice -a --keyring-backend test) \
  <your-metamask-address> \
  1000000000ubadge \
  --chain-id bitbadges \
  --keyring-backend test \
  --yes

# Option 2: Use the chain's genesis accounts if configured
```

## Simple dApp Setup

### Project Structure

A typical dApp project structure:

```
my-dapp/
├── contracts/              # Solidity contracts
│   ├── MyContract.sol
│   └── interfaces/
│       └── ITokenizationPrecompile.sol
├── scripts/               # Deployment scripts
│   └── deploy.ts
├── app/                   # Frontend (Next.js, React, etc.)
│   └── page.tsx
└── package.json
```

### Basic Contract Template

Here's a minimal contract that uses the tokenization precompile:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./interfaces/ITokenizationPrecompile.sol";
import "./types/TokenizationTypes.sol";

contract MyTokenContract {
    // Precompile address
    ITokenizationPrecompile constant precompile = 
        ITokenizationPrecompile(0x0000000000000000000000000000000000001001);
    
    uint256 public collectionId;
    
    constructor(uint256 _collectionId) {
        collectionId = _collectionId;
    }
    
    function transfer(
        address to,
        uint256 amount,
        uint256 tokenId
    ) external returns (bool) {
        address[] memory recipients = new address[](1);
        recipients[0] = to;
        
        TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
        tokenIds[0] = TokenizationTypes.UintRange({
            start: tokenId,
            end: tokenId
        });
        
        TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
        ownershipTimes[0] = TokenizationTypes.UintRange({
            start: 1,
            end: type(uint256).max
        });
        
        return precompile.transferTokens(
            collectionId,
            recipients,
            amount,
            tokenIds,
            ownershipTimes
        );
    }
    
    function getBalance(address user) external view returns (uint256) {
        TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
        tokenIds[0] = TokenizationTypes.UintRange({
            start: 1,
            end: type(uint256).max
        });
        
        TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
        ownershipTimes[0] = TokenizationTypes.UintRange({
            start: 1,
            end: type(uint256).max
        });
        
        return precompile.getBalanceAmount(collectionId, user, tokenIds, ownershipTimes);
    }
}
```

### Deployment Script (TypeScript/ethers.js)

```typescript
import { ethers } from "ethers";
import * as fs from "fs";

async function deploy() {
  // Connect to EVM JSON-RPC
  const provider = new ethers.JsonRpcProvider("http://localhost:8545");
  
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
  
  const contract = await contractFactory.deploy(collectionId);
  await contract.waitForDeployment();
  
  const address = await contract.getAddress();
  console.log("Contract deployed at:", address);
  
  // Save deployment info
  fs.writeFileSync(
    "deployed.json",
    JSON.stringify({ address, abi: CONTRACT_ABI }, null, 2)
  );
}

deploy().catch(console.error);
```

### Frontend Integration (React/ethers.js)

```typescript
import { ethers } from "ethers";
import { useState, useEffect } from "react";

export function useContract() {
  const [contract, setContract] = useState<ethers.Contract | null>(null);
  const [provider, setProvider] = useState<ethers.BrowserProvider | null>(null);
  
  useEffect(() => {
    if (typeof window.ethereum !== "undefined") {
      const provider = new ethers.BrowserProvider(window.ethereum);
      setProvider(provider);
      
      // Load deployed contract
      const contractAddress = "0x..."; // Your deployed contract address
      const contract = new ethers.Contract(
        contractAddress,
        CONTRACT_ABI,
        await provider.getSigner()
      );
      setContract(contract);
    }
  }, []);
  
  const transfer = async (to: string, amount: bigint, tokenId: bigint) => {
    if (!contract) throw new Error("Contract not loaded");
    const tx = await contract.transfer(to, amount, tokenId);
    await tx.wait();
  };
  
  return { contract, provider, transfer };
}
```

## Contract Snippets

### Helper Library

The repository includes a comprehensive helper library at `contracts/libraries/TokenizationHelpers.sol` that provides utility functions for:

- Creating `UintRange` structs (single values, sequences, full ranges)
- Creating `Balance`, `CollectionMetadata`, `TokenMetadata` structs
- Creating empty permission structures
- Validating ranges and balances
- Common patterns for ownership times and token IDs

**Import and use the helper library:**

```solidity
import "./libraries/TokenizationHelpers.sol";

contract MyContract {
    function example() external {
        // Create a single token ID range
        TokenizationTypes.UintRange memory tokenId = 
            TokenizationHelpers.createSingleTokenIdRange(123);
        
        // Create a full ownership time range (1 to max uint256)
        TokenizationTypes.UintRange memory fullTime = 
            TokenizationHelpers.createFullOwnershipTimeRange();
        
        // Create a token ID sequence (range from start to end)
        TokenizationTypes.UintRange memory sequence = 
            TokenizationHelpers.createTokenIdSequence(1, 100);
        
        // Create a UintRange array from arrays
        uint256[] memory starts = new uint256[](2);
        uint256[] memory ends = new uint256[](2);
        starts[0] = 1; ends[0] = 10;
        starts[1] = 20; ends[1] = 30;
        TokenizationTypes.UintRange[] memory ranges = 
            TokenizationHelpers.createUintRangeArray(starts, ends);
    }
}
```

See the [TokenizationHelpers.sol](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts/libraries/TokenizationHelpers.sol) file for all available helper functions.

### Common Patterns

#### Pattern 1: Simple Token Transfer

```solidity
ITokenizationPrecompile constant precompile = 
    ITokenizationPrecompile(0x0000000000000000000000000000000000001001);

function simpleTransfer(
    uint256 collectionId,
    address to,
    uint256 amount,
    uint256 tokenId
) external returns (bool) {
    address[] memory recipients = new address[](1);
    recipients[0] = to;
    
    TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
    tokenIds[0] = TokenizationHelpers.createSingleTokenIdRange(tokenId);
    
    TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
    ownershipTimes[0] = TokenizationHelpers.createFullOwnershipTimeRange();
    
    return precompile.transferTokens(
        collectionId,
        recipients,
        amount,
        tokenIds,
        ownershipTimes
    );
}
```

#### Pattern 2: Batch Transfer

```solidity
ITokenizationPrecompile constant precompile = 
    ITokenizationPrecompile(0x0000000000000000000000000000000000001001);

function batchTransfer(
    uint256 collectionId,
    address[] calldata recipients,
    uint256[] calldata amounts,
    uint256 tokenId
) external returns (bool) {
    require(recipients.length == amounts.length, "Arrays length mismatch");
    
    // Transfer to each recipient
    for (uint256 i = 0; i < recipients.length; i++) {
        address[] memory singleRecipient = new address[](1);
        singleRecipient[0] = recipients[i];
        
        TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
        tokenIds[0] = TokenizationHelpers.createSingleTokenIdRange(tokenId);
        
        TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
        ownershipTimes[0] = TokenizationHelpers.createFullOwnershipTimeRange();
        
        precompile.transferTokens(
            collectionId,
            singleRecipient,
            amounts[i],
            tokenIds,
            ownershipTimes
        );
    }
    
    return true;
}
```

#### Pattern 3: Time-Limited Transfer

```solidity
ITokenizationPrecompile constant precompile = 
    ITokenizationPrecompile(0x0000000000000000000000000000000000001001);

function transferWithExpiration(
    uint256 collectionId,
    address to,
    uint256 amount,
    uint256 tokenId,
    uint256 expirationTime
) external returns (bool) {
    address[] memory recipients = new address[](1);
    recipients[0] = to;
    
    TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
    ownershipTimes[0] = TokenizationTypes.UintRange({
        start: block.timestamp,
        end: expirationTime
    });
    
    TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
    tokenIds[0] = TokenizationHelpers.createSingleTokenIdRange(tokenId);
    
    return precompile.transferTokens(
        collectionId,
        recipients,
        amount,
        tokenIds,
        ownershipTimes
    );
}
```

#### Pattern 4: Using Helper Library

```solidity
import "./libraries/TokenizationHelpers.sol";

contract HelperExample {
    ITokenizationPrecompile constant precompile = 
        ITokenizationPrecompile(0x0000000000000000000000000000000000001001);
    
    function transferWithHelpers(
        uint256 collectionId,
        address to,
        uint256 amount,
        uint256 tokenId
    ) external returns (bool) {
        address[] memory recipients = new address[](1);
        recipients[0] = to;
        
        // Use helper library for cleaner code
        TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
        tokenIds[0] = TokenizationHelpers.createSingleTokenIdRange(tokenId);
        
        TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
        ownershipTimes[0] = TokenizationHelpers.createFullOwnershipTimeRange();
        
        return precompile.transferTokens(
            collectionId,
            recipients,
            amount,
            tokenIds,
            ownershipTimes
        );
    }
}
```

#### Pattern 5: Dynamic Store for Compliance

```solidity
contract ComplianceToken {
    ITokenizationPrecompile constant precompile = 
        ITokenizationPrecompile(0x0000000000000000000000000000000000001001);
    
    uint256 public kycRegistryId;
    uint256 public collectionId;
    
    function initialize(uint256 _collectionId) external {
        collectionId = _collectionId;
        
        // Create KYC registry (default: false = not KYC'd)
        kycRegistryId = precompile.createDynamicStore(
            false,
            "ipfs://...", // URI
            "" // customData
        );
    }
    
    function setKYC(address user, bool isKYCd) external {
        precompile.setDynamicStoreValue(kycRegistryId, user, isKYCd);
    }
    
    function isKYCd(address user) public view returns (bool) {
        bytes memory result = precompile.getDynamicStoreValue(kycRegistryId, user);
        return abi.decode(result, (bool));
    }
    
    function transfer(address to, uint256 amount, uint256 tokenId) external {
        require(isKYCd(msg.sender), "Sender not KYC'd");
        require(isKYCd(to), "Recipient not KYC'd");
        
        // Proceed with transfer...
    }
}
```

## Complete Examples

### Counter dApp

For a complete end-to-end example, see the [counter-dapp](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp) in the repository. It includes:

- Simple Solidity contract (Counter.sol)
- Deployment script (TypeScript)
- Next.js frontend with MetaMask integration
- Complete setup instructions

### ERC-3643 Example Contracts

The [contracts/examples](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts/examples) directory contains production-ready examples:

1. **TwoFactorSecurityToken.sol** - Security token with 2FA authentication
2. **RealEstateSecurityToken.sol** - Tokenized real estate with KYC/AML
3. **CarbonCreditToken.sol** - Carbon credits with vintage tracking
4. **PrivateEquityToken.sol** - Private equity fund tokens with lock-ups

Each example demonstrates:
- Dynamic Stores for compliance registries
- Time-bound ownership for lock-ups and expirations
- Complex approval systems
- Real-world use cases

See the [examples README](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts/examples/README.md) for detailed explanations of each contract.

## Troubleshooting

### RPC Connection Issues

**Problem**: Can't connect to RPC endpoint

**Solutions**:
- Ensure the chain is running: `bitbadgeschaind start --json-rpc.enable --json-rpc.address 0.0.0.0:8545`
- Check if EVM JSON-RPC is enabled in `app.toml`
- Try both ports: `http://localhost:8545` (EVM) and `http://localhost:26657` (Tendermint)
- Verify the chain is fully synced

### MetaMask Connection Issues

**Problem**: MetaMask can't connect to the network

**Solutions**:
- Use the correct Chain ID:
  - `90123` for local development
  - `50025` for testnet
  - `50024` for mainnet
- Use EVM JSON-RPC port: `http://localhost:8545` (not 26657) for local development
- Ensure your local chain is running (for local development)
- Check MetaMask console for detailed error messages

### Transaction Failures

**Problem**: Transactions fail with "insufficient funds" or revert

**Solutions**:
- Fund your account with BADGE tokens:
  ```bash
  bitbadgeschaind tx bank send <validator> <your-address> 1000000000ubadge --keyring-backend test
  ```
- Check gas prices (may need to adjust in MetaMask)
- Verify approvals are set correctly for token transfers
- Check contract logs for specific error messages

### Contract Deployment Issues

**Problem**: Contract deployment fails

**Solutions**:
- Ensure you have sufficient balance for deployment gas
- Check that the EVM module is enabled
- Verify your Solidity version matches the chain's supported version
- Check deployment script logs for specific errors

## Next Steps

- Review the [Developer Guide](developer-guide.md) for essential information
- Explore [Contract Examples](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts/examples) for real-world patterns
- Check the [Precompile API Reference](tokenization-precompile/API.md) for all available methods
- See the [Counter dApp](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp) for a complete working example


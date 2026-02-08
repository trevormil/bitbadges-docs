# Getting Started with EVM Precompiles

> ⚠️ **Important Pre-Reading: Production Status**
> 
> **The EVM integration is not currently deployed on BitBadges mainnet.**
> 
> 1. **Development Status**: The EVM implementation is available on the `evm-poc` branch of the [bitbadgeschain repository](https://github.com/BitBadges/bitbadgeschain).
> 
> 2. **Testing Options**:
>    - You can run it locally by checking out the `evm-poc` branch
>    - Reach out to us to set up a dedicated testnet if that would be easier for your use case
>    - For a full end-to-end example, see the [counter-dapp directory](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp) in the evm-poc repository - a simple counter dApp demonstrating complete integration
>    - See the [contracts folder](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts) for examples, the precompile interface, and helper libraries
> 
> 3. **Code Status & Licensing**:
>    - The implementation is **fully code complete** and functional
>    - We are open to discussing licensing models or other collaboration ideas
>    - **Deploying EVM on BitBadges mainnet is not in our short-term plans**
> 
> Please keep this in mind when evaluating the EVM integration for your projects.

This guide will help you get started using the BitBadges EVM precompiles in your Solidity smart contracts.

## Prerequisites

- Basic knowledge of Solidity
- Understanding of Ethereum smart contracts
- Familiarity with Cosmos SDK concepts (helpful but not required)

## Installation

### 1. Import the Precompile Interface

First, import the precompile interface in your Solidity contract:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./interfaces/ITokenizationPrecompile.sol";
import "./types/TokenizationTypes.sol";
```

### 2. Initialize the Precompile

Create an instance of the precompile at its fixed address:

```solidity
contract MyTokenContract {
    ITokenizationPrecompile constant precompile = 
        ITokenizationPrecompile(0x0000000000000000000000000000000000001001);
    
    // Your contract code here
}
```

## Basic Example: Transfer Tokens

Here's a simple example of transferring tokens:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./interfaces/ITokenizationPrecompile.sol";
import "./types/TokenizationTypes.sol";

contract SimpleTransfer {
    ITokenizationPrecompile constant precompile = 
        ITokenizationPrecompile(0x0000000000000000000000000000000000001001);
    
    function transferTokens(
        uint256 collectionId,
        address recipient,
        uint256 amount,
        uint256 tokenId
    ) external returns (bool) {
        // Prepare recipients array
        address[] memory recipients = new address[](1);
        recipients[0] = recipient;
        
        // Prepare token ID range
        TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
        tokenIds[0] = TokenizationTypes.UintRange({
            start: tokenId,
            end: tokenId
        });
        
        // Prepare ownership time range (full range)
        TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
        ownershipTimes[0] = TokenizationTypes.UintRange({
            start: 1,
            end: type(uint256).max
        });
        
        // Execute transfer
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

## Basic Example: Query Balance

Query a user's balance:

```solidity
function getBalance(
    uint256 collectionId,
    address user
) external view returns (bytes memory) {
    // Query balance (returns protobuf-encoded bytes)
    return precompile.getBalance(collectionId, user);
}
```

## Understanding Types

The precompile uses comprehensive type definitions that match the Cosmos SDK proto types. Key types include:

- `UintRange`: Represents a range of token IDs or ownership times
- `Balance`: Represents a user's balance with token IDs and ownership times
- `CollectionMetadata`: Collection metadata (URI, custom data)
- `ApprovalCriteria`: Complex approval conditions

See [Types & Data Structures](tokenization-precompile/types.md) for complete type reference.

## Error Handling

All precompile methods can revert with structured errors. Handle errors appropriately:

```solidity
function safeTransfer(
    uint256 collectionId,
    address recipient,
    uint256 amount
) external {
    try precompile.transferTokens(...) returns (bool success) {
        require(success, "Transfer failed");
    } catch Error(string memory reason) {
        // Handle error
        revert(reason);
    }
}
```

See [Error Handling](tokenization-precompile/errors.md) for error codes and handling strategies.

## Next Steps

- Read the [Tokenization Precompile Overview](tokenization-precompile/overview.md)
- Explore [Transaction Methods](tokenization-precompile/transactions.md)
- Review [Query Methods](tokenization-precompile/queries.md)
- Check out [Examples](examples/README.md) for more complex use cases

## Common Patterns

### Batch Transfers

Transfer to multiple recipients:

```solidity
address[] memory recipients = new address[](3);
recipients[0] = address1;
recipients[1] = address2;
recipients[2] = address3;

precompile.transferTokens(
    collectionId,
    recipients,
    amount,
    tokenIds,
    ownershipTimes
);
```

### Token ID Ranges

Transfer multiple token IDs:

```solidity
TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](2);
tokenIds[0] = TokenizationTypes.UintRange({start: 1, end: 100});
tokenIds[1] = TokenizationTypes.UintRange({start: 200, end: 300});
```

### Time-Based Ownership

Specify ownership time ranges:

```solidity
TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
ownershipTimes[0] = TokenizationTypes.UintRange({
    start: block.timestamp,
    end: block.timestamp + 365 days
});
```

## Resources

- [Cosmos SDK EVM Documentation](https://docs.cosmos.network/evm/v0.5.0/documentation/overview)
- [Tokenization Module Architecture](../x-tokenization/README.md)
- [Example Contracts](../contracts/examples/)
- [Counter dApp Example](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp) - Full end-to-end counter dApp in the evm-poc repository
- [Contracts Folder](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts) - Examples, precompile interface, and helper libraries










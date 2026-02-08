# Tokenization Precompile

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

The Tokenization Precompile provides full access to the BitBadges tokenization module from Solidity smart contracts.

## Precompile Address

```
0x0000000000000000000000000000000000001001
```

## Overview

The tokenization precompile enables Solidity contracts to:

- **Transfer tokens** with complex approval systems
- **Create and manage collections** of digital assets
- **Query balances** with time-based ownership
- **Manage approvals** (incoming/outgoing) with sophisticated criteria
- **Use dynamic stores** for flexible data storage
- **Participate in governance** through voting challenges

## Quick Start

```solidity
import "./interfaces/ITokenizationPrecompile.sol";

ITokenizationPrecompile constant precompile = 
    ITokenizationPrecompile(0x0000000000000000000000000000000000001001);

// Transfer tokens
bool success = precompile.transferTokens(
    collectionId,
    recipients,
    amount,
    tokenIds,
    ownershipTimes
);
```

## Features

### Transaction Methods (24+)

- **Transfers**: `transferTokens`
- **Approvals**: `setIncomingApproval`, `setOutgoingApproval`, `deleteIncomingApproval`, `deleteOutgoingApproval`
- **Collections**: `createCollection`, `updateCollection`, `deleteCollection`, `universalUpdateCollection`
- **Metadata**: `setCollectionMetadata`, `setTokenMetadata`, `setCustomData`
- **Management**: `setManager`, `setValidTokenIds`, `setStandards`, `setIsArchived`
- **Address Lists**: `createAddressLists`
- **Dynamic Stores**: `createDynamicStore`, `updateDynamicStore`, `deleteDynamicStore`, `setDynamicStoreValue`
- **Governance**: `castVote`, `purgeApprovals`, `updateUserApprovals`

### Query Methods (15+)

- **Collections**: `getCollection`
- **Balances**: `getBalance`, `getBalanceAmount`, `getTotalSupply`, `getWrappableBalances`
- **Approvals**: `getApprovalTracker`
- **Challenges**: `getChallengeTracker`, `getETHSignatureTracker`
- **Address Lists**: `getAddressList`
- **Dynamic Stores**: `getDynamicStore`, `getDynamicStoreValue`
- **Voting**: `getVote`, `getVotes`
- **Protocol**: `isAddressReservedProtocol`, `getAllReservedProtocolAddresses`
- **Parameters**: `params`

## Documentation

- [Overview](overview.md) - Detailed overview and features
- [Installation](installation.md) - Setup and configuration
- [API Reference](api-reference.md) - Complete API documentation
- [Transaction Methods](transactions.md) - All transaction methods
- [Query Methods](queries.md) - All query methods
- [Types & Data Structures](types.md) - Type definitions
- [Error Handling](errors.md) - Error codes and handling
- [Gas & Costs](gas.md) - Gas calculation and costs
- [Security](security.md) - Security best practices
- [Examples](examples.md) - Code examples

## Integration

The precompile integrates seamlessly with:

- **Cosmos SDK EVM Module**: Uses standard EVM precompile interface
- **Tokenization Module**: Direct access to tokenization keeper
- **Type System**: Full type compatibility with proto definitions

For Cosmos SDK EVM integration details, see the [official documentation](https://docs.cosmos.network/evm/v0.5.0/documentation/overview).

## Resources

- [Getting Started Guide](../getting-started.md)
- [Architecture Documentation](../architecture.md)
- [Example Contracts](../../contracts/examples/)
- [Tokenization Module Documentation](../../../_docs/TOKENIZATION_MODULE_ARCHITECTURE.md)
- [Counter dApp Example](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp) - Full end-to-end counter dApp in the evm-poc repository
- [Contracts Folder](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts) - Examples, precompile interface, and helper libraries










# EVM Precompiles

> ⚠️ **Production Status**: EVM integration is not deployed on mainnet. Available on `evm-poc` branch. See [EVM Integration](../EVM_INTEGRATION.md) for details.

## What are Precompiles?

Precompiles are special contract addresses that execute native Go code instead of EVM bytecode, providing:

- Direct access to Cosmos SDK modules
- Full type conversion between Solidity and Cosmos SDK
- Built-in validation and error handling
- Optimized gas costs

## Available Precompiles

### Tokenization Precompile

**Address:** `0x0000000000000000000000000000000000001001`

Provides full access to the BitBadges tokenization module for token transfers, collection management, approvals, dynamic stores, and governance.

[Tokenization Precompile Documentation →](tokenization-precompile/README.md)

## Documentation

- **[Setup & Configuration](setup-and-configuration.md)** - Chain config, MetaMask setup, dApp setup
- **[Developer Guide](developer-guide.md)** - Transaction signing, address conversion, limitations
- **[Architecture](architecture.md)** - System architecture
- **[Tokenization Precompile](tokenization-precompile/README.md)** - Precompile documentation

## Resources

- [Cosmos SDK EVM Documentation](https://docs.cosmos.network/evm/v0.5.0/documentation/overview)
- [Tokenization Module](../../x-badges/README.md)
- [Counter dApp Example](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp)
- [Contracts Folder](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts)

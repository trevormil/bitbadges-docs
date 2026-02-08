# EVM Integration

> ⚠️ **Important: Production Status**
> 
> **The EVM integration is not currently deployed on BitBadges mainnet.**
> 
> - Available on the `evm-poc` branch of the [bitbadgeschain repository](https://github.com/BitBadges/bitbadgeschain)
> - Can be run locally or testnet setup available upon request
> - Fully code complete and open to licensing discussions
> - **Mainnet deployment is not in our short-term plans**
> 
> See [counter-dapp](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp) and [contracts](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts) for examples.

## Overview

BitBadges Chain integrates the Cosmos `x/evm` module to enable Ethereum Virtual Machine (EVM) compatibility. Solidity contracts can interact with the tokenization module through precompiled contracts.

## Architecture

```
Solidity Contract → EVM Precompile → Tokenization Module → Cosmos SDK State
```

**Precompile Address:** `0x0000000000000000000000000000000000001001`

## Documentation

- **[Setup & Configuration](evm-precompiles/setup-and-configuration.md)** - Chain config, MetaMask setup, dApp setup
- **[Developer Guide](evm-precompiles/developer-guide.md)** - Transaction signing, address conversion, limitations
- **[Architecture](evm-precompiles/architecture.md)** - System architecture details
- **[Tokenization Precompile API](evm-precompiles/tokenization-precompile/API.md)** - Complete API reference
- **[Error Handling](evm-precompiles/tokenization-precompile/errors.md)** - Error codes and handling
- **[Gas Costs](evm-precompiles/tokenization-precompile/gas.md)** - Gas calculation and optimization
- **[Security](evm-precompiles/tokenization-precompile/security.md)** - Security best practices

## Resources

- [Cosmos SDK EVM Documentation](https://docs.cosmos.network/evm/v0.5.0/documentation/overview)
- [Tokenization Module](../../x-badges/README.md)
- [Counter dApp Example](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp)
- [Contracts Folder](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts)

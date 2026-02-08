# Tokenization Precompile

> ⚠️ **Production Status**: EVM integration is not deployed on mainnet. Available on `evm-poc` branch.

**Address:** `0x0000000000000000000000000000000000001001`

Provides full access to the BitBadges tokenization module from Solidity contracts.

## Quick Start

```solidity
import "./interfaces/ITokenizationPrecompile.sol";

ITokenizationPrecompile constant precompile = 
    ITokenizationPrecompile(0x0000000000000000000000000000000000001001);

bool success = precompile.transferTokens(
    collectionId, recipients, amount, tokenIds, ownershipTimes
);
```

## Capabilities

- **Transfers**: Token transfers with approval systems
- **Collections**: Create and manage collections
- **Queries**: Balance and collection queries
- **Approvals**: Incoming/outgoing approval management
- **Dynamic Stores**: Flexible key-value storage
- **Governance**: Voting and challenge systems

## Documentation

- **[API Reference](API.md)** - Complete API documentation
- **[Error Handling](errors.md)** - Error codes and handling
- **[Gas Costs](gas.md)** - Gas calculation and optimization
- **[Security](security.md)** - Security best practices

## Resources

- [Setup & Configuration](../setup-and-configuration.md)
- [Developer Guide](../developer-guide.md)
- [Example Contracts](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts)
- [Counter dApp](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp)

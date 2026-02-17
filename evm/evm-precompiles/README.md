# EVM Precompiles

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

**Features:**
- Single and multi-message execution
- Collection creation and management
- Token transfers with approvals
- Dynamic stores for custom data
- Governance voting

[Tokenization Precompile Documentation →](tokenization-precompile/README.md)

### GAMM Precompile

**Address:** `0x0000000000000000000000000000000000001002`

Provides access to liquidity pools for joining, exiting, and swapping tokens.

**Features:**
- Join and exit liquidity pools
- Single and multi-hop swaps
- IBC transfers with swaps
- Pool information queries
- Calculation queries

[GAMM Precompile Documentation →](gamm-precompile/README.md) | [API Reference →](gamm-precompile/API.md) | [Common Gotchas →](gamm-precompile/gotchas.md)

### SendManager Precompile

**Address:** `0x0000000000000000000000000000000000001003`

Send native Cosmos coins directly from EVM without ERC20 wrapping.

**Features:**
- Send native Cosmos coins from EVM
- No ERC20 wrapping required
- Supports alias denoms (e.g., `badgeslp:...`)
- All accounting in `x/bank` (Cosmos side)

[SendManager Precompile Documentation →](sendmanager-precompile/README.md)

### Cosmos SDK Precompiles

Default precompiles from [cosmos/evm](https://github.com/cosmos/evm) for interacting with Cosmos SDK modules.

| Precompile | Address | Description |
|------------|---------|-------------|
| P256 | `0x...0100` | secp256r1 signature verification |
| Bech32 | `0x...0400` | Address format conversion |
| Staking | `0x...0800` | Delegate, undelegate, redelegate |
| Distribution | `0x...0801` | Claim staking rewards |
| ICS20 | `0x...0802` | IBC token transfers |
| Vesting | `0x...0803` | Vesting account management |
| Bank | `0x...0804` | Query balances and supply |
| Governance | `0x...0805` | Vote on proposals |
| Slashing | `0x...0806` | Unjail validators |

[Cosmos SDK Precompiles Documentation →](cosmos-precompiles.md)

## Documentation

- **[Setup & Configuration](setup-and-configuration.md)** - Chain config, MetaMask setup, dApp setup
- **[Developer Guide](developer-guide.md)** - Transaction signing, address conversion, limitations
- **[Architecture](architecture.md)** - System architecture

### Precompile Documentation
- **[Tokenization Precompile](tokenization-precompile/README.md)** - Token transfers, collections, approvals
- **[GAMM Precompile](gamm-precompile/README.md)** - Liquidity pools, swaps
- **[SendManager Precompile](sendmanager-precompile/README.md)** - Native coin transfers
- **[Cosmos SDK Precompiles](cosmos-precompiles.md)** - Staking, distribution, governance, IBC

## Resources

- [Cosmos SDK EVM Documentation](https://docs.cosmos.network/evm/v0.5.0/documentation/overview)
- [Tokenization Module](../../x-tokenization/README.md)
- [Counter dApp Example](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp)
- [Contracts Folder](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts)

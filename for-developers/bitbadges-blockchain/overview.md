# Overview

BitBadges offers an L1 delegated proof-of-stake blockchain built with [Cosmos SDK](https://docs.cosmos.network/main). The blockchain is able to attain instant transaction finality using Tendermint and supports both Cosmos-based 'bb' prefixed bech32 addresses with Cosmos signatures, as well as Ethereum-compatible addresses (0x-prefixed) with EVM signatures through precompile contracts.

The source code can be found at [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain).&#x20;

BitBadges defines its official metadata, IBC connections, and more in the Cosmos chain registry at  [https://github.com/cosmos/chain-registry](https://github.com/cosmos/chain-registry).

## IBC Connections

BitBadges (`bitbadges-1`) maintains the following IBC transfer channels, all
registered in the chain registry's [`_IBC`](https://github.com/cosmos/chain-registry/tree/master/_IBC)
directory and all ACTIVE:

| Peer chain | BitBadges channel | Peer channel | Connection (BitBadges side) | Notable assets |
|------------|-------------------|--------------|-----------------------------|----------------|
| Osmosis | `channel-0` | `channel-104311` | `connection-1` | OSMO; BADGE liquidity on Osmosis |
| Noble | `channel-2` | `channel-158` | `connection-6` | Legacy `USDC.n` (backwards compatibility only) |
| Cosmos Hub | `channel-3` | `channel-1420` | `connection-8` | ATOM |
| Injective | `channel-40` | `channel-464` | `connection-89` | Canonical `USDC` — Circle's native `USDC.inj` (`erc20:0xa00C59fF5a080D2b954d0c75e46E22a0c371235a`), arriving as `ibc/E1116484...` |

The Injective connection ([`_IBC/bitbadges-injective.json`](https://github.com/cosmos/chain-registry/blob/master/_IBC/bitbadges-injective.json))
carries the canonical USDC denom — one IBC hop from Injective's native USDC.
See [Supported Denoms](supported-denoms.md) for the full denom table and the
canonical-vs-legacy USDC policy.

Other Links

[https://explorer.bitbadges.io](https://explorer.bitbadges.io/)

**Cosmos SDK Endpoints:**
- [https://rpc.bitbadges.io](https://rpc.bitbadges.io) - Tendermint RPC
- [https://lcd.bitbadges.io](https://lcd.bitbadges.io) - REST/LCD API

**EVM JSON-RPC Endpoints:**
- [https://evm-rpc.bitbadges.io](https://evm-rpc.bitbadges.io) - Ethereum-compatible JSON-RPC

For detailed information about EVM RPC endpoints, see [EVM RPC Endpoints](evm-rpc-endpoints.md).


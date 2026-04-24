# Run a Mainnet Node

This page walks you through starting a BitBadges mainnet validator or full node from scratch. Follow the steps in order; each one is a command you can copy and run.

If you already run Cosmos SDK chains, the flow will feel familiar. If you get stuck, ask in the `#validators` channel of our [Discord](https://discord.com/invite/TJMaEd9Kar) — ping `@trevormil` for the Validator role.

> As a validator you are responsible for the security and uptime of the network. Take normal production precautions (firewalls, monitoring, sentry nodes, key management) to avoid slashing or losing staked funds.

## 1. Install the binary

See the [CLI Installation guide](../cli/installation.md) for the one-line installer and build-from-source instructions. When you are done you should have `bitbadgeschaind` on your `PATH`:

```bash
bitbadgeschaind version
```

## 2. Initialize your node

Pick a moniker (the public name of your node) and run `init`. This creates `~/.bitbadgeschain/` with a default `config/config.toml`, `config/app.toml`, and a placeholder `config/genesis.json`.

```bash
bitbadgeschaind init <moniker> --chain-id bitbadges-1
```

## 3. Download the canonical genesis file

Replace the freshly-generated placeholder genesis with the pinned mainnet genesis (post-711316 hard fork):

```bash
curl -L https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/genesis-711316.json \
  -o ~/.bitbadgeschain/config/genesis.json
```

Sanity-check the file is non-empty and parses as JSON before continuing.

## 4. Configure peers

Open `~/.bitbadgeschain/config/config.toml` and set `persistent_peers` (and/or `seeds`) to the addresses of known-good mainnet nodes. You need at least one working peer for your node to find the rest of the network.

```toml
# ~/.bitbadgeschain/config/config.toml
persistent_peers = "<nodeID>@<host>:<port>,<nodeID>@<host>:<port>"
```

> **First-party peer list:** Pending — reach out in the `#validators` Discord channel to request current peer addresses while a canonical list is being prepared. Most active validators also publish their own peer IDs in their setup guides (see the community guides linked at the bottom of this page).

## 5. Set the required consensus parameter

`timeout_commit` must match the rest of the network or your node will fall out of sync. The default from `init` is `5s`; mainnet uses `2s`.

In `~/.bitbadgeschain/config/config.toml`:

```toml
# ~/.bitbadgeschain/config/config.toml
timeout_commit = "2s"
```

You can also do this non-interactively:

```bash
sed -i 's/^timeout_commit = "5s"/timeout_commit = "2s"/' \
  ~/.bitbadgeschain/config/config.toml
```

## 6. Set the EVM chain ID

BitBadges exposes Ethereum-compatible JSON-RPC. The `evm-chain-id` in `app.toml` must match the network. Mainnet is `50024`; testnet is `50025`. The default after `init` is wrong for both.

In `~/.bitbadgeschain/config/app.toml`:

```toml
[evm]
evm-chain-id = 50024
```

If you do not set this correctly, wallets such as MetaMask will reject transactions with `incorrect chain-id`. See [EVM JSON-RPC Configuration](#evm-json-rpc-configuration) below for the full set of EVM-related settings.

## 7. Start the node

```bash
bitbadgeschaind start
```

Watch the logs. You should see block heights ticking up within a minute or two once peers are connected. If you stay at height `0` or log `No addresses added` for more than a few minutes, your peer list is wrong — revisit step 4.

## 8. Verify sync

Query the local node for its current block height and catch-up status:

```bash
bitbadgeschaind status 2>&1 | jq '.sync_info'
```

`catching_up: false` means you are fully synced with the rest of the network. Compare `latest_block_height` against the [block explorer](https://explorer.bitbadges.io/BitBadges%20Mainnet/staking) to confirm.

## 9. (Optional) Speed things up with a snapshot

A full sync from genesis can take hours. To shortcut it, restore a state snapshot from a community validator:

- [provewithryd — Network Overview and snapshots](https://docs.provewithryd.xyz/mainnet/bitbadges/network-overview)
- [nodestake — BitBadges snapshot](https://nodestake.org/bitbadges)

Most validators also publish their own guides; pick whichever one matches your OS and preferences. Restore the snapshot **after** steps 1–6 but **before** step 7 (`start`).

## 10. (Optional) Run under Cosmovisor

We announce upgrades in the `#chain-upgrades` Discord channel and use the Cosmos SDK `x/upgrade` module. Running under [Cosmovisor](https://docs.cosmos.network/main/tooling/cosmovisor) lets upgrades happen automatically at the scheduled block height. A minimal setup:

```bash
export DAEMON_HOME=$HOME/.bitbadgeschain
export DAEMON_NAME=bitbadgeschaind
cosmovisor init $(which bitbadgeschaind)
cosmovisor run start
```

For anything more advanced (download-upgrade, backup policy), follow the [official Cosmovisor docs](https://docs.cosmos.network/main/tooling/cosmovisor) with `bitbadgeschaind` substituted in for the example daemon.

## Useful references

- [Binary releases (GitHub)](https://github.com/BitBadges/bitbadgeschain/releases)
- [Canonical genesis file](https://github.com/BitBadges/bitbadgeschain/blob/master/genesis-711316.json)
- [Block explorer](https://explorer.bitbadges.io/BitBadges%20Mainnet/staking)
- [BitBadges public RPC](https://rpc.bitbadges.io)
- [Cosmos SDK — Running a Node](https://docs.cosmos.network/main/user/run-node/run-node)
- [Cosmos Tutorials — Path to Production](https://tutorials.cosmos.network/tutorials/9-path-to-prod/1-overview.html)

## Community guides

If you want a second reference while setting up, these community validator guides cover similar ground:

- [provewithryd — BitBadges network overview](https://docs.provewithryd.xyz/mainnet/bitbadges/network-overview)
- [nodestake — BitBadges](https://nodestake.org/bitbadges)

Most active validators in the `#validators` Discord channel have their own write-ups as well.

## Running a Relayer?

See the official IBC connections in the Cosmos chain registry that we support.

## EVM JSON-RPC Configuration

If you want to expose Ethereum-compatible JSON-RPC endpoints (for MetaMask, ethers.js, etc.), configure the EVM settings in your `app.toml`:

### Critical: EVM Chain ID

⚠️ **IMPORTANT**: You **must** set the `evm-chain-id` in `app.toml` to match the network:

```toml
[evm]
# CRITICAL: Set this to match your network's EVM chain ID
# Mainnet: 50024
# Testnet: 50025
# The default value (90123) will cause wallet transaction failures!
evm-chain-id = 50024
```

**Why this matters:** The `evm-chain-id` setting is used by the `net_version` RPC method for EIP-155 signature verification. If this doesn't match `eth_chainId` (which reads from chain state), MetaMask and other wallets will fail with error: `incorrect chain-id; expected 50024, got 90123`.

### JSON-RPC Server Options

Configure the JSON-RPC server in `app.toml`:

```toml
[json-rpc]
# Enable JSON-RPC server
enable = true

# Address to listen on (use 0.0.0.0:8545 for external access)
address = "127.0.0.1:8545"

# WebSocket address for subscriptions
ws-address = "127.0.0.1:8546"

# API namespaces to enable
api = ["eth", "net", "web3"]

# Allow unprotected (non EIP-155) transactions
allow-unprotected-txs = false

# Enable custom tx indexer for better query performance
enable-indexer = true

# Maximum requests in a batch
batch-request-limit = 1000

# Maximum server response size (bytes)
batch-response-max-size = 25000000

# Max block range for eth_getLogs queries
block-range-cap = 10000

# Max results from eth_getLogs
logs-cap = 10000

# Timeout for eth_call (0 = infinite)
evm-timeout = "5s"

# Global filter cap
filter-cap = 200

# Gas cap for eth_call/estimateGas (0 = infinite)
gas-cap = 25000000

# Transaction fee cap (in BADGE)
txfee-cap = 1.0

# HTTP timeouts
http-timeout = "30s"
http-idle-timeout = "2m0s"

# Maximum simultaneous connections (0 = unlimited)
max-open-connections = 0
```

### Full Command-Line Options

All JSON-RPC options can also be passed as command-line flags:

| Flag | Default | Description |
|------|---------|-------------|
| `--json-rpc.enable` | `false` | Enable JSON-RPC server |
| `--json-rpc.address` | `127.0.0.1:8545` | HTTP server address |
| `--json-rpc.ws-address` | `127.0.0.1:8546` | WebSocket server address |
| `--json-rpc.api` | `eth,net,web3` | Enabled API namespaces |
| `--json-rpc.enable-indexer` | `false` | Enable custom tx indexer |
| `--json-rpc.evm-timeout` | `5s` | Timeout for eth_call |
| `--json-rpc.gas-cap` | `25000000` | Gas cap for calls |
| `--json-rpc.txfee-cap` | `1.0` | Transaction fee cap |
| `--json-rpc.filter-cap` | `200` | Max active filters |
| `--json-rpc.block-range-cap` | `10000` | Max block range for logs |
| `--json-rpc.logs-cap` | `10000` | Max log results |
| `--json-rpc.batch-request-limit` | `1000` | Max batch requests |
| `--json-rpc.batch-response-max-size` | `25000000` | Max response size |
| `--json-rpc.http-timeout` | `30s` | HTTP read/write timeout |
| `--json-rpc.http-idle-timeout` | `2m0s` | HTTP idle timeout |
| `--json-rpc.max-open-connections` | `0` | Max connections |
| `--json-rpc.allow-unprotected-txs` | `false` | Allow non-EIP155 txs |
| `--json-rpc.ws-origins` | `127.0.0.1,localhost` | WebSocket allowed origins |

### Production Recommendations

For production nodes serving EVM RPC:

1. **Use a reverse proxy** (nginx, caddy) in front of the JSON-RPC server for TLS termination and rate limiting
2. **Set appropriate gas/fee caps** to prevent resource exhaustion
3. **Enable the indexer** (`--json-rpc.enable-indexer`) for better query performance
4. **Configure WebSocket origins** if accepting external WS connections
5. **Monitor resource usage** — JSON-RPC can be resource-intensive under load

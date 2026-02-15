# Run a Mainnet Node

Running a node is the same as any other Cosmos SDK chain. If you encounter any issues during the setup process, you can also refer to other Cosmos SDK node documentation, such as "[Cosmos SDK - Running a Node](https://docs.cosmos.network/main/user/run-node/run-node)" or "[Cosmos Tutorials - Run in Production documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/1-overview.html)."&#x20;

Below are some links to get you started. If you are unfamiliar with setup, I'd recommend following a guide and/or asking for help in the #validators channel of our Discord. We have plenty of great validators maintaining the network, and many have detailed guides to help you get started. LLMs are also a good resource and can help you get set up.

Note that as a validator, you are responsible for the security of the network. Take necessary precautions for uptime and security in order to avoid slashing or losing staked funds.

**Discord**

Our main communication method is via Discord:

* \#validators channel is a great resource for help. Ping @trevormil to give you the Validator role.
* \#announcements and #chain-upgrades are where we announce the latest plans to upgrade. We use the x/upgrade module from Cosmos SDK. We recommend Cosmovisor for handling upgrades.

**Guides + Setup w/ Snapshots**

* [https://docs.provewithryd.xyz/mainnet/bitbadges/network-overview](https://docs.provewithryd.xyz/mainnet/bitbadges/network-overview)
* [https://nodestake.org/bitbadges](https://nodestake.org/bitbadges)
* Note that most validators have their own guides. Choose whichever one works best for you.

**Releases / Instructions**

* [https://github.com/BitBadges/bitbadgeschain/releases](https://github.com/BitBadges/bitbadgeschain/releases)

**Genesis File**

* [https://github.com/BitBadges/bitbadgeschain/blob/master/genesis-711316.json](https://github.com/BitBadges/bitbadgeschain/blob/master/genesis-711316.json) (Note this is post 711316 hard fork)

**Explorer**

* [https://explorer.bitbadges.io/BitBadges%20Mainnet/staking](https://explorer.bitbadges.io/BitBadges%20Mainnet/staking)

**BitBadges RPC**

* https://rpc.bitbadges.io

**Running a Relayer?**

* See the official IBC connections in the Cosmos chain registry that we support.

**Configuration**

Most node parameters are flexible and you can set up according to your preferences. Here are some that should be the same as the rest of the network.&#x20;

* chain\_id: bitbadges-1
* timeout\_commit: 2s

## EVM JSON-RPC Configuration

If you want to expose Ethereum-compatible JSON-RPC endpoints (for MetaMask, ethers.js, etc.), you need to configure the EVM settings in your `app.toml`:

### Critical: EVM Chain ID

⚠️ **IMPORTANT**: You **must** set the `evm-chain-id` in `app.toml` to match the network:

```toml
[evm]
# CRITICAL: Set this to match your network's EVM chain ID
# Mainnet: 50024
# Testnet: 50025
# The default value (262144) will cause wallet transaction failures!
evm-chain-id = 50024
```

**Why this matters:** The `evm-chain-id` setting is used by the `net_version` RPC method for EIP-155 signature verification. If this doesn't match `eth_chainId` (which reads from chain state), MetaMask and other wallets will fail with error: `incorrect chain-id; expected 262144, got 50024`.

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
5. **Monitor resource usage** - JSON-RPC can be resource-intensive under load

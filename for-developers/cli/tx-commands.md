# Tx Commands

The `tx` subcommand confirms whether a broadcast transaction landed on chain. It hits the chain's REST/RPC endpoints directly — Cosmos LCD first, with automatic fall-through to the EVM JSON-RPC for `eth_sendRawTransaction`-broadcast hashes. No indexer round-trip.

Use `tx` to close the loop after `bitbadges-cli deploy` (or any external broadcast).

## Subcommands

```bash
bitbadges-cli tx status <hash>                 # one-shot status fetch
bitbadges-cli tx wait   <hash> [--timeout 60s] # poll until committed/failed
```

## `tx status <hash>`

Fetches one transaction by hash. Tries the Cosmos LCD's `/cosmos/tx/v1beta1/txs/{hash}` endpoint first; if the hash isn't recognized there, falls through to the EVM JSON-RPC's `eth_getTransactionReceipt` to handle keccak256 hashes from EVM-routed broadcasts.

```bash
bitbadges-cli tx status 0F46899E29754227DE90F702754CDC74FD39EA37527F2C1E307655C15E910D81 --mainnet
# Status:    committed  (code 0)
# Via:       Cosmos LCD
# Hash:      0F46899E...
# Height:    10000000
# Gas used:  156490 / 237670
# Timestamp: 2026-05-01T04:54:56Z
```

`--format json` returns the full envelope:

```json
{
  "ok": true,
  "data": {
    "via": "cosmos",
    "hash": "0F46899E...",
    "height": "10000000",
    "code": 0,
    "gasUsed": "156490",
    "events": [...]
  },
  "warnings": [],
  "error": null
}
```

For successful EVM-routed transactions, `via` is `"evm"` and the data shape includes the same fields populated from `eth_getTransactionReceipt`.

### Exit codes

| Code | Meaning |
|---|---|
| `0` | Transaction committed (Cosmos `code === 0` or EVM `status === '0x1'`) |
| `1` | Transaction included on chain but failed (non-zero Cosmos code or EVM revert) |
| `2` | RPC error or transaction not found via either endpoint |

Pair with `tx wait` (below) when the tx may not be indexed yet.

### Hash format

Both Cosmos sha256 and EVM keccak256 hashes are accepted, with or without a `0x` prefix, in any case. The CLI normalizes to upper-case hex internally:

```bash
bitbadges-cli tx status 0xabcdef...    # works
bitbadges-cli tx status ABCDEF...      # works
bitbadges-cli tx status abcdef...      # works
```

### Network selection

Same flags as the rest of the CLI: `--mainnet | --testnet | --local | --url <api-url> | --node-url <chain-lcd-url>`. The `--node-url` override is useful when querying a private chain at a custom LCD endpoint.

For EVM hashes, the CLI uses the matching EVM RPC URL from `NETWORK_CONFIGS[network].evmRpcUrl`. Override via `--evm-rpc-url <url>`.

## `tx wait <hash>`

Polls the same endpoints until the transaction is found and committed (or fails). Use after `deploy` when the tx hash is fresh and may not be indexed yet.

```bash
bitbadges-cli tx wait $TXHASH --mainnet --timeout 120 --format json
```

| Flag | Default | Description |
|---|---|---|
| `--timeout <seconds>` | `60` | Max seconds to wait before exiting non-zero |
| `--interval <seconds>` | `2` | Polling interval |
| `--format <json\|text>` | TTY-dependent | Output format |

### Exit codes

Same as `tx status`, plus:

- `2` is also returned on timeout. The error envelope includes `error.code === "timeout"` and a `hint:` pointing at re-running `tx status` later or extending `--timeout`.

```bash
bitbadges-cli tx wait 1111111... --mainnet --timeout 5 --format json
# {
#   "ok": false,
#   "data": null,
#   "warnings": [],
#   "hint": "Re-run `tx status 1111111...` later, or extend with --timeout <seconds>.",
#   "error": {
#     "code": "timeout",
#     "message": "Timed out after 5s waiting for 1111111...",
#     "details": {"hash": "1111111...", "timeoutSeconds": 5}
#   }
# }
```

## Worked example: deploy → wait → confirm

```bash
# 1. Build a vault collection
bitbadges-cli build vault --backing-coin USDC --name "My Vault" \
    --image https://... --description "..." \
    --json-only > vault.json

# 2. Dry-run before spending
bitbadges-cli deploy vault.json --burner --dry-run --manager bb1... --mainnet

# 3. Real deploy
RESULT=$(bitbadges-cli deploy vault.json --burner --manager bb1... --mainnet --format json)
TXHASH=$(echo "$RESULT" | jq -r '.data.txHash')

# 4. Wait for it to commit
bitbadges-cli tx wait "$TXHASH" --mainnet --timeout 60
```

## Why no indexer route?

The BitBadges indexer doesn't expose a public `tx-by-hash` query. Hitting the chain RPC directly is the canonical way to confirm a tx — same path the indexer's own poller uses internally. This keeps `tx` working even when the indexer is degraded or in a regional outage.

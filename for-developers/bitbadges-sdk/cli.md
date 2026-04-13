# BitBadges CLI

> **This page has moved.** The canonical CLI documentation is now at [CLI & Chain Binary](../cli/). See [Installation](../cli/installation.md), [SDK Commands](../cli/sdk-commands.md), and [API Commands](../cli/api-commands.md).

The BitBadges CLI provides SDK utilities and API access from your terminal. It ships with the SDK package and is also available via the chain binary.

## Installation

```bash
npm install -g bitbadgesjs-sdk
```

This installs the `bitbadges-cli` command globally. Verify:

```bash
bitbadges-cli --help
```

### Via bitbadgeschaind

The chain binary delegates `sdk` and `api` subcommands to the Node.js CLI automatically:

```bash
bitbadgeschaind sdk review tx.json
bitbadgeschaind api tokens get-collection 1
```

Discovery order:

1. `BITBADGES_SDK_CLI_PATH` environment variable
2. `bitbadges-cli` on PATH
3. `npx -p bitbadgesjs-sdk bitbadges-cli` as fallback

## Configuration

### Config file

The CLI reads `~/.bitbadges/config.json`. Manage it with the `config` command:

```bash
# Set values
bitbadges-cli config set apiKey YOUR_KEY
bitbadges-cli config set apiKeyTestnet YOUR_TESTNET_KEY
bitbadges-cli config set apiKeyLocal YOUR_LOCAL_KEY
bitbadges-cli config set network testnet
bitbadges-cli config set url https://custom-api.example.com/api/v0

# View current config
bitbadges-cli config show

# Remove a value
bitbadges-cli config unset apiKeyTestnet
```

Supported keys: `apiKey`, `apiKeyTestnet`, `apiKeyLocal`, `network` (`mainnet` | `testnet` | `local`), `url`.

### Environment variables

| Variable | Description |
|---|---|
| `BITBADGES_API_KEY` | Default API key (all networks) |
| `BITBADGES_API_KEY_TESTNET` | Testnet-specific API key |
| `BITBADGES_API_KEY_LOCAL` | Local-specific API key |
| `BITBADGES_API_URL` | Custom base URL (overrides config) |

### Network flags

| Flag | Description |
|---|---|
| `--testnet` | Use testnet API (`https://api.testnet.bitbadges.io`) |
| `--local` | Use local API (`http://localhost:3001`) |
| `--url <url>` | Custom API base URL (overrides all others) |

API key resolution order: `--api-key` flag > network-specific env var > `BITBADGES_API_KEY` env var > network-specific config key > default config `apiKey`.

Base URL resolution order: `--url` flag > `--local` > `--testnet` > `BITBADGES_API_URL` env > config file > default production URL.

## SDK Commands

### review

Run validation, audit, and standards checks on a transaction or collection. Accepts a JSON file, inline JSON, collection ID (numeric), or `-` for stdin.

```bash
bitbadges-cli sdk review tx.json
bitbadges-cli sdk review collection.json --human
bitbadges-cli sdk review 42 --testnet
echo '{"messages":[...]}' | bitbadges-cli sdk review -
```

If `BITBADGES_API_KEY` is set, transactions are sent to the enriched simulate endpoint. Falls back to local analysis via the unified `reviewCollection()` entry point if the API is unavailable.

For collection JSON (no `messages` field), runs `reviewCollection()` locally.

`reviewCollection()` composes three check families — `audit` (permission/supply/centralization), `standards` (protocol conformance), and `ux` (the deterministic UX checks ported from the frontend review sidebar) — into a single `Finding[]`. Each finding carries an `audience` field (`'agent' | 'human' | 'both'`) so consumers can filter: agents get everything; the frontend sidebar passes `audienceFilter: 'human'` via `ReviewContext` to hide audit findings the MCP validators already surface during construction.

### interpret-tx

Generate a human-readable explanation of a transaction.

```bash
bitbadges-cli sdk interpret-tx tx.json
bitbadges-cli sdk interpret-tx '{"messages":[...]}' --output-file summary.txt
```

### interpret-collection

Generate a human-readable explanation of a collection. Accepts a JSON file, inline JSON, numeric collection ID (fetched via API), or `-` for stdin.

```bash
bitbadges-cli sdk interpret-collection collection.json
bitbadges-cli sdk interpret-collection 42 --testnet
```

### address convert

Convert addresses between `bb1` (Cosmos) and `0x` (Ethereum) formats.

```bash
bitbadges-cli sdk address convert 0x1234...abcd --to bb1
bitbadges-cli sdk address convert bb1qpzm... --to 0x
```

### address validate

Check if an address is valid and detect its chain type.

```bash
bitbadges-cli sdk address validate bb1qpzm...
# { "valid": true, "chain": "BitBadges", "address": "bb1qpzm..." }
```

### lookup-token

Look up token info by symbol. Returns IBC denom, decimals, supported networks, and backing address (for IBC tokens). Omit the symbol to list all known tokens.

```bash
bitbadges-cli sdk lookup-token          # list all tokens
bitbadges-cli sdk lookup-token BADGE    # specific token info
```

### alias for-ibc-backing

Generate the backing address for an IBC-backed smart token.

```bash
bitbadges-cli sdk alias for-ibc-backing ibc/27394FB092D2ECCD...
```

### alias for-wrapper

Generate the wrapper path address for a cosmos coin wrapper.

```bash
bitbadges-cli sdk alias for-wrapper ubadge
```

### alias for-mint-escrow

Generate the mint escrow address for a collection (where quest reward funds are sent, etc.).

```bash
bitbadges-cli sdk alias for-mint-escrow 42
```

### gen-list-id

Generate a reserved address list ID from a set of addresses.

```bash
bitbadges-cli sdk gen-list-id bb1abc... bb1def...
bitbadges-cli sdk gen-list-id bb1abc... --blacklist
```

### skills

List available Builder skills or fetch a specific skill's instructions from the bundled docs.

```bash
bitbadges-cli sdk skills               # list all skills
bitbadges-cli sdk skills smart-token   # show specific skill
```

### docs

Browse bundled BitBadges documentation.

```bash
bitbadges-cli sdk docs                      # show section tree
bitbadges-cli sdk docs all                  # dump full documentation
bitbadges-cli sdk docs learn                # specific section
bitbadges-cli sdk docs learn/approvals      # navigate deeper with slash paths
```

### status

Show CLI status: active API key (masked), network, base URL, config path, SDK version, and API health check.

```bash
bitbadges-cli sdk status
bitbadges-cli sdk status --testnet
```

## API Commands

The `api` subcommand provides access to all 104+ indexer API routes, auto-generated from the OpenAPI spec. Routes are grouped by category.

### Route groups

| Group | Description |
|---|---|
| `api accounts` | Account and user routes |
| `api tokens` | Collection and token routes |
| `api claims` | Claim management routes |
| `api auth` | Sign In with BitBadges / OAuth routes |
| `api tx` | Transaction broadcast and simulation |
| `api apps` | Developer apps and applications |
| `api plugins` | Plugin management routes |
| `api stores` | Dynamic data store routes |
| `api onchain-stores` | On-chain dynamic store routes |
| `api pages` | Utility page routes |
| `api maps` | On-chain map and protocol routes |
| `api assets` | DEX, pools, and asset pair routes |
| `api misc` | Miscellaneous routes |
| `api all` | All routes ungrouped (flat list) |

### Usage

```bash
bitbadges-cli api <group> <route-name> [path-params...] [options]
```

Path parameters are positional arguments. Request bodies can be inline JSON, a file reference with `@`, or `-` for stdin.

### Common flags

| Flag | Description |
|---|---|
| `--body <json>` | Request body: inline JSON, `@file.json`, or `-` for stdin |
| `--query <json>` | Query string params as a JSON object |
| `--api-key <key>` | Override the API key for this request |
| `--testnet` | Use testnet API |
| `--local` | Use local API (localhost:3001) |
| `--url <url>` | Custom API base URL |
| `--condensed` | Output condensed JSON (no whitespace) |
| `--dry-run` | Show request details without sending |
| `--output-file <path>` | Write output to file instead of stdout |

### Examples

```bash
# GET routes
bitbadges-cli api tokens get-collection 1
bitbadges-cli api accounts get-account --body '{"address":"bb1..."}'

# POST routes (with body)
bitbadges-cli api accounts get-accounts --body '{"accountsToFetch":[{"address":"bb1..."}]}'
bitbadges-cli api tx broadcast-tx --body @tx.json

# GET routes auto-convert --body to query params
bitbadges-cli api accounts get-tokens-for-user bb1... --body '{"viewType":"collected"}'

# Testnet with dry-run
bitbadges-cli api tokens get-collection 1 --testnet --dry-run

# List routes in a group
bitbadges-cli api tokens --help
```

### Help and type schemas

Each route's `--help` includes the HTTP method, path template, SDK type names, key body/query fields, and an SDK code example (where available).

```bash
bitbadges-cli api tokens get-collections --help
```

## Global Options

| Flag | Description |
|---|---|
| `--help-json` | Output the full command tree as structured JSON (useful for LLMs) |
| `--help` | Standard help output |

### Shell completion

Generate a bash/zsh completion script:

```bash
eval "$(bitbadges-cli completion)"
```

Add that line to your `.bashrc` or `.zshrc` for persistent completions.

## Build Commands

Token building (creating collections, generating approvals, etc.) is handled by the [BitBadges Builder Tools](../ai-agents/builder-tools.md), not the CLI directly. The CLI focuses on analysis, review, and API access.

## bitbadgeschaind Delegation

The Go chain binary includes `sdk` and `api` subcommands that delegate to the Node.js CLI:

```bash
# These are equivalent:
bitbadgeschaind sdk review tx.json
bitbadges-cli sdk review tx.json

bitbadgeschaind api tokens get-collection 1
bitbadges-cli api tokens get-collection 1
```

The binary looks for the CLI in this order:

1. `BITBADGES_SDK_CLI_PATH` environment variable
2. `bitbadges-cli` on PATH
3. `npx -p bitbadgesjs-sdk bitbadges-cli` as fallback

If the CLI is not found, the chain binary prints an install message but does not error -- chain operations (keys, tx, query) continue to work.

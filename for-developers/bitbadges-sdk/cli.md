# BitBadges CLI

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
bitbadgeschaind api get-collection 1
```

If `bitbadges-cli` is not on your PATH, the binary falls back to `npx`. You can also set `BITBADGES_SDK_CLI_PATH` to point to a specific binary location.

## Configuration

| Environment Variable | Description |
|---|---|
| `BITBADGES_API_KEY` | Required for API commands and enriched review |

| Flag | Description |
|---|---|
| `--testnet` | Use testnet API (`https://api.bitbadges.io/testnet`) |
| `--local` | Use local API (`http://localhost:3001`) |

## SDK Commands

### review

Run validation, audit, and standards checks on a transaction or collection JSON file.

```bash
bitbadges-cli sdk review tx.json
bitbadges-cli sdk review collection.json --human
```

If `BITBADGES_API_KEY` is set, this calls the enriched simulate endpoint for transactions, returning `validationErrors`, `auditFindings`, and `standardsCompliance`. Falls back to local analysis if the API is unavailable.

For collection JSON (no `messages` field), runs `auditCollection` and `verifyStandardsCompliance` locally.

### interpret-tx

Generate a human-readable explanation of a `MsgUniversalUpdateCollection` transaction.

```bash
bitbadges-cli sdk interpret-tx tx.json
```

### interpret-collection

Generate a human-readable explanation of a collection JSON.

```bash
bitbadges-cli sdk interpret-collection collection.json
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

### alias for-denom / alias for-collection

Generate alias addresses for IBC denoms and collection escrows.

```bash
bitbadges-cli sdk alias for-denom ibc/27394FB092D2ECCD...
bitbadges-cli sdk alias for-collection 42
```

### gen-list-id

Generate a reserved address list ID from a set of addresses.

```bash
bitbadges-cli sdk gen-list-id bb1abc... bb1def...
bitbadges-cli sdk gen-list-id bb1abc... --blacklist
```

### skills list / skills get

List and fetch MCP builder skill instructions.

```bash
bitbadges-cli sdk skills list
bitbadges-cli sdk skills get smart-token
```

### docs

Fetch the full BitBadges documentation in LLM-friendly format.

```bash
bitbadges-cli sdk docs
```

## API Commands

The `api` subcommand provides access to all 104+ indexer API routes, auto-generated from the OpenAPI spec.

### Usage Pattern

```bash
bitbadges-cli api <route-name> [path-params...] [--body <json-or-file>] [--testnet] [--local]
```

Path parameters are positional arguments. Request bodies can be inline JSON or a file reference with `@`.

### Examples

```bash
# GET routes (no body)
bitbadges-cli api get-status
bitbadges-cli api get-collection 1

# POST routes (with body)
bitbadges-cli api get-accounts --body '{"accountsToFetch":[{"address":"bb1..."}]}'
bitbadges-cli api broadcast-tx --body @tx.json

# Testnet
bitbadges-cli api get-collection 1 --testnet
```

### List Available Routes

```bash
bitbadges-cli api --help
```

## Build Commands

Token building (creating collections, generating approvals, etc.) is handled by the [MCP Builder Tools](../ai-agents/mcp-builder-tools.md), not the CLI directly. The CLI focuses on analysis, review, and API access.

## bitbadgeschaind Delegation

The Go chain binary includes `sdk` and `api` subcommands that delegate to the Node.js CLI:

```bash
# These are equivalent:
bitbadgeschaind sdk review tx.json
bitbadges-cli sdk review tx.json

bitbadgeschaind api get-collection 1
bitbadges-cli api get-collection 1
```

The binary looks for the CLI in this order:
1. `BITBADGES_SDK_CLI_PATH` environment variable
2. `bitbadges-cli` on PATH
3. `npx bitbadges-cli` as fallback

If the CLI is not found, the chain binary prints an install message but does not error — chain operations (keys, tx, query) continue to work.

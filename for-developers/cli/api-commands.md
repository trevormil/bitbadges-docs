# API Commands

The `api` subcommand provides access to all 104+ indexer API routes, auto-generated from the OpenAPI spec. Routes are grouped by category. Available via both `bitbadges-cli api` and `bitbadgeschaind cli api`.

## Route Groups

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

## Usage

```bash
bitbadges-cli api <group> <route-name> [path-params...] [options]
```

Path parameters are positional arguments. Request bodies can be inline JSON, a file reference with `@`, or `-` for stdin.

## Common Flags

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

## Examples

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

## Help and Type Schemas

Each route's `--help` includes the HTTP method, path template, SDK type names, key body/query fields, and an SDK code example (where available).

```bash
bitbadges-cli api tokens get-collections --help
```

## Discovery: `--search` and `--schema`

Two flags help agents and humans navigate the 100+ routes without grepping the help tree.

### `--search <keyword>`

Substring scan over route name, path, tag, and description. Case-insensitive. Default text columns; pass `--format json` for a parseable list.

```bash
bitbadges-cli api --search swap
# estimate-swap          POST  /api/{version}/swaps/estimate
#       Estimate Swap
# get-swap-activities    GET   /swapActivities
#       Get Swap Activities

bitbadges-cli api --search balance --format json
# [{"name":"get-balance-by-address-specific-token", ...}, ...]
```

### `--schema` (per route)

Print the route's request body fields, query parameters, and SDK type names without making an API call. Useful for agents that want to construct valid request bodies offline.

```bash
bitbadges-cli api tokens get-collection --schema
# {
#   "name": "get-collection",
#   "method": "GET",
#   "path": "/collection/{collectionId}",
#   "sdkLinks": { "response": "iGetCollectionSuccessResponse", ... },
#   "queryParams": [],
#   "bodyFields": []
# }
```

`--schema` works on every route in every group.

## Authentication Hints on Errors

When an `api` call fails with HTTP 401 or 403 (typically a Full Access route called without a session), the CLI emits a one-liner pointing at the recovery action:

```bash
bitbadges-cli api accounts get-account --body '{"address":"bb1..."}'
# Error: API error: Unauthorized
# Hint: This route requires user-scoped auth — run `bitbadges-cli auth login`, then retry with `--with-session`.
```

If the call already attached a cookie via `--with-session` and the indexer still refused, the hint suggests a re-login (session likely expired or scoped wrong).

## Global Options

| Flag | Description |
|---|---|
| `--help-json` | Output the full command tree as structured JSON (useful for LLMs) |
| `--quiet` / `BB_QUIET=1` | Silence stderr commentary; errors still emit |
| `--help` | Standard help output |

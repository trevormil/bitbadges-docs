# Utility Commands

Catch-all reference for the CLI's smaller utility verbs — discovery, address tooling, lookup, and connectivity.

| Command | Purpose |
|---|---|
| [`bb dev docs [section]`](#dev-docs) | Browse BitBadges documentation locally |
| [`bb dev skills [id]`](#dev-skills) | Shorthand for `bb dev docs builder-skills` — list or show Builder skills |
| [`bb account convert \| validate`](#account-convert-validate) | Address conversion and validation |
| [`bb account alias for-ibc-backing \| for-wrapper \| for-mint-escrow`](#account-alias) | Generate protocol-derived alias addresses |
| [`bb account lookup [symbol]`](#account-lookup) | Look up token info by symbol |
| [`bb account gen-list-id <addresses...>`](#account-gen-list-id) | Generate a reserved address list ID |
| [`bb doctor`](#doctor) | Environment + connectivity health check |

> All six address / lookup verbs sit under `bb account` so the top-level help is the place to discover anything account-flavored. The `bb account` umbrella also covers `bb account all`, `bb account balances`, `bb account approvals`, etc. — the former `portfolio` aggregator.

## dev docs

Browse BitBadges documentation. Fetched from GitHub on first use, cached locally for 24 hours.

```bash
bb dev docs                               # show section tree
bb dev docs all                           # dump full corpus
bb dev docs learn                         # specific section
bb dev docs learn/approval-criteria       # nested with slash
bb dev docs learn/approval-criteria/merkle-challenges
bb dev docs --refresh                     # force cache refresh
```

Partial matching: `bb dev docs approvals` finds the first section containing "approvals".

| Flag | Description |
|---|---|
| `--refresh` | Force refresh the cached docs corpus |

Cache: `~/.bitbadges/docs-cache.json`.

## dev skills

Shorthand for `bb dev docs builder-skills` — list or show one of the BitBadges Builder skills.

```bash
bb dev skills                             # list all skills
bb dev skills smart-token                 # specific skill
```

Equivalent to `bb dev docs builder-skills` / `bb dev docs builder-skills/<id>`.

## account convert / validate

Convert and validate BitBadges addresses.

```bash
bb account convert 0x1234...abcd --to bb1
bb account convert bb1qpzm... --to 0x
bb account validate bb1qpzm...
# { "valid": true, "chain": "BitBadges", "address": "bb1qpzm..." }
```

| Subcommand | Flag | Description |
|---|---|---|
| `convert <address>` | `--to <bb1\|0x>` | Convert to the target format |
| `validate <address>` | — | Check validity and detect chain |

`bb1...` ↔ `0x...` is deterministic (same key, two encodings). The CLI handles the bech32/hex math.

## account alias

Generate protocol-derived alias addresses — used wherever the chain auto-derives an account from input data (IBC denoms, collection IDs, etc.).

```bash
bb account alias for-ibc-backing ibc/27394FB092D2ECCD...
bb account alias for-wrapper ubadge
bb account alias for-mint-escrow 42
```

| Subcommand | Purpose |
|---|---|
| `for-ibc-backing <ibcDenom>` | Backing address for an IBC-backed smart token (deposits land here) |
| `for-wrapper <denom>` | Wrapper path address for a Cosmos coin wrapper |
| `for-mint-escrow <collectionId>` | Mint-escrow address for a collection (where quest reward funds, etc. are sent) |

These are protocol-controlled addresses with auto-set approvals. Don't try to write to them directly — use the corresponding flow (IBC backing, mint, etc.).

## account lookup

Look up token info by symbol. Returns IBC denom, decimals, supported networks, and backing address (for IBC tokens). Omit the symbol to list every known token.

```bash
bb account lookup                             # list every token
bb account lookup BADGE                       # specific token
```

| Flag | Description |
|---|---|
| `--output-file <path>` | Write the JSON output to a file |

## account gen-list-id

Generate a reserved address list ID from a set of addresses. Used to deterministically reference an ad-hoc allow/deny list without registering it on-chain first.

```bash
bb account gen-list-id bb1abc... bb1def...
bb account gen-list-id bb1abc... --blacklist
```

| Flag | Description |
|---|---|
| `--blacklist` | Treat as blacklist (default is whitelist) |

## doctor

One-shot environment + connectivity health check. Replaces the old `sdk status` and `builder doctor` commands.

```bash
bb doctor                             # six core probes
bb doctor --testnet                   # probe against testnet
bb doctor --json                      # machine-readable
bb doctor --with-preview              # add the preview-roundtrip probe
```

**Probes:**

1. Node version (must be ≥ 18)
2. SDK package + version (loaded from the bitbadges package.json)
3. CLI config file at `~/.bitbadges/config.json`
4. API key reachable for the resolved network (pings `/api/v0/simulate`)
5. MCP stdio bin presence
6. Persisted sessions parse
7. **(`--with-preview` only)** Preview upload + fetch roundtrip — builds a minimal tx, POSTs it to `/api/v0/builder/preview`, GETs it back, asserts the round trip is byte-equivalent

| Flag | Description |
|---|---|
| `--json` | Output the full DoctorReport as JSON |
| `--with-preview` | Add the preview-roundtrip probe |
| `--testnet`, `--local`, `--url` | Network selection — controls which indexer the API-key probe hits |

Each probe reports `PASS` / `FAIL` / `WARN` / `SKIP`. Exits non-zero only on hard failures; warnings and skips are informational.

## See also

- [Build Commands](build-commands.md), [Analysis Commands](analysis-commands.md), [Deploy Commands](deploy-commands.md), [Tool Commands](tool-commands.md) — the rest of the CLI surface.
- [Auth Commands](auth-commands.md) — for endpoints that need a user-scoped session cookie on top of the API key.

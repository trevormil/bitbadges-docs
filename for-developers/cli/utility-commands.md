# Utility Commands

Catch-all reference for the CLI's smaller utility verbs — discovery, address tooling, lookup, and connectivity.

| Command | Purpose |
|---|---|
| [`docs [section]`](#docs) | Browse BitBadges documentation locally |
| [`skills [id]`](#skills) | Shorthand for `docs builder-skills` — list or show Builder skills |
| [`address convert \| validate`](#address) | Address conversion and validation |
| [`alias for-ibc-backing \| for-wrapper \| for-mint-escrow`](#alias) | Generate protocol-derived alias addresses |
| [`lookup [symbol]`](#lookup) | Look up token info by symbol |
| [`gen-list-id <addresses...>`](#gen-list-id) | Generate a reserved address list ID |
| [`doctor`](#doctor) | Environment + connectivity health check |

## docs

Browse BitBadges documentation. Fetched from GitHub on first use, cached locally for 24 hours.

```bash
bitbadges-cli docs                               # show section tree
bitbadges-cli docs all                           # dump full corpus
bitbadges-cli docs learn                         # specific section
bitbadges-cli docs learn/approval-criteria       # nested with slash
bitbadges-cli docs learn/approval-criteria/merkle-challenges
bitbadges-cli docs --refresh                     # force cache refresh
```

Partial matching: `bitbadges-cli docs approvals` finds the first section containing "approvals".

| Flag | Description |
|---|---|
| `--refresh` | Force refresh the cached docs corpus |

Cache: `~/.bitbadges/docs-cache.json`.

## skills

Shorthand for `docs builder-skills` — list or show one of the BitBadges Builder skills.

```bash
bitbadges-cli skills                             # list all skills
bitbadges-cli skills smart-token                 # specific skill
```

Equivalent to `docs builder-skills` / `docs builder-skills/<id>`.

## address

Convert and validate BitBadges addresses.

```bash
bitbadges-cli address convert 0x1234...abcd --to bb1
bitbadges-cli address convert bb1qpzm... --to 0x
bitbadges-cli address validate bb1qpzm...
# { "valid": true, "chain": "BitBadges", "address": "bb1qpzm..." }
```

| Subcommand | Flag | Description |
|---|---|---|
| `convert <address>` | `--to <bb1\|0x>` | Convert to the target format |
| `validate <address>` | — | Check validity and detect chain |

`bb1...` ↔ `0x...` is deterministic (same key, two encodings). The CLI handles the bech32/hex math.

## alias

Generate protocol-derived alias addresses — used wherever the chain auto-derives an account from input data (IBC denoms, collection IDs, etc.).

```bash
bitbadges-cli alias for-ibc-backing ibc/27394FB092D2ECCD...
bitbadges-cli alias for-wrapper ubadge
bitbadges-cli alias for-mint-escrow 42
```

| Subcommand | Purpose |
|---|---|
| `for-ibc-backing <ibcDenom>` | Backing address for an IBC-backed smart token (deposits land here) |
| `for-wrapper <denom>` | Wrapper path address for a Cosmos coin wrapper |
| `for-mint-escrow <collectionId>` | Mint-escrow address for a collection (where quest reward funds, etc. are sent) |

These are protocol-controlled addresses with auto-set approvals. Don't try to write to them directly — use the corresponding flow (IBC backing, mint, etc.).

## lookup

Look up token info by symbol. Returns IBC denom, decimals, supported networks, and backing address (for IBC tokens). Omit the symbol to list every known token.

```bash
bitbadges-cli lookup                             # list every token
bitbadges-cli lookup BADGE                       # specific token
```

| Flag | Description |
|---|---|
| `--output-file <path>` | Write the JSON output to a file |

## gen-list-id

Generate a reserved address list ID from a set of addresses. Used to deterministically reference an ad-hoc allow/deny list without registering it on-chain first.

```bash
bitbadges-cli gen-list-id bb1abc... bb1def...
bitbadges-cli gen-list-id bb1abc... --blacklist
```

| Flag | Description |
|---|---|
| `--blacklist` | Treat as blacklist (default is whitelist) |

## doctor

One-shot environment + connectivity health check. Replaces the old `sdk status` and `builder doctor` commands.

```bash
bitbadges-cli doctor                             # six core probes
bitbadges-cli doctor --testnet                   # probe against testnet
bitbadges-cli doctor --json                      # machine-readable
bitbadges-cli doctor --with-preview              # add the preview-roundtrip probe
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

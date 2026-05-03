# Analysis Commands

Once you have a transaction JSON in hand (from `build`, the MCP builder tools, or hand-rolled), the next step is to inspect it. The CLI ships four analysis verbs that all accept the same input shapes (JSON file path, `@file.json` token, inline JSON, numeric collection id where applicable, or `-` for stdin):

| Command | Purpose |
|---|---|
| [`check <input>`](#check) | Validate / review / metadata coverage in one pass — the unified analysis verb |
| [`explain <input>`](#explain) | Plain-English summary, auto-detects tx vs collection |
| [`simulate <input>`](#simulate) | Dry-run against the indexer's simulate endpoint, returns gas + per-address balance changes |
| [`preview <input>`](#preview) | Upload to the indexer and print a shareable bitbadges.io preview URL |

`check` and `doctor` (covered on the [Utility Commands](utility-commands.md) page) replace the old `sdk review` / `builder verify` / `sdk status` / `builder doctor` quartet.

## check

`bitbadges-cli check` is the unified analysis entry point. It folds the old `validate`, `review`, and `verify` verbs into one command with a `--depth` dial.

```bash
bitbadges-cli check tx.json                       # full check (default)
bitbadges-cli check tx.json --depth structural    # validateTransaction() only
bitbadges-cli check tx.json --depth review        # reviewCollection() only
bitbadges-cli check 42 --depth review --testnet   # live collection, design audit
echo '{"messages":[...]}' | bitbadges-cli check -
```

**Depth levels:**

| `--depth` | What it runs | Use when |
|---|---|---|
| `structural` | `validateTransaction()` only — uint ranges, approval criteria, structural correctness | Fast offline check before you commit JSON to disk |
| `review` | `reviewCollection()` only — design audit, standards conformance, UX checks | You're reviewing someone else's work or a live collection by id |
| `full` (default) | validate + review + design + metadata coverage, all rendered together | Pre-broadcast diligence; the most useful single-shot check |

**Common flags:**

| Flag | Description |
|---|---|
| `--depth <level>` | `structural \| review \| full` (default: `full`) |
| `--json` | Output the structured result as JSON |
| `--strict` | Exit `1` on warnings (criticals always exit `2`) |
| `--no-validate`, `--no-review`, `--no-metadata` | Skip the corresponding section in `--depth full` |
| `--design` | Include the design-decisions section in `--depth full` (informational ✓/✗) |
| `--output-file <path>` | Write the rendered output to a file (ANSI stripped) |
| `--testnet`, `--local`, `--url` | Network selection for fetching numeric collection ids |

`check` accepts collection ids on `review` and `full` depths (fetches the collection from the indexer first). `structural` is offline-only and refuses numeric ids — pass tx JSON directly.

## explain

`bitbadges-cli explain` produces a plain-English summary. Auto-detects whether the input is a transaction or a collection and routes to the right interpreter.

```bash
bitbadges-cli explain tx.json
bitbadges-cli explain '{"messages":[...]}'
bitbadges-cli explain 42 --testnet           # numeric: fetch collection from indexer
echo '{"messages":[...]}' | bitbadges-cli explain -
```

**Input detection:**

- Single Msg `{ typeUrl, value: {...} }` → walks `value` through `interpretTransaction()`
- Tx wrapper `{ messages: [{ typeUrl, value }, ...] }` → finds the first collection msg and explains it
- Raw collection (no `typeUrl`, no `messages`) → walks through `interpretCollection()`
- Numeric `<n>` → fetches `/api/v0/collection/<n>` then walks through `interpretCollection()`

**Flags:**

| Flag | Description |
|---|---|
| `--output-file <path>` | Write the output to a file instead of stdout |
| `--testnet`, `--local`, `--url` | Network selection for numeric collection-id fetches |

Replaces the old `sdk interpret-tx` and `sdk interpret-collection` commands plus `builder explain` — one verb, auto-detect, one less mental dimension.

## simulate

`bitbadges-cli simulate` dry-runs a transaction against the indexer's `/api/v0/simulate` endpoint. Returns the parsed events, per-address balance changes, and any errors that would have surfaced on-chain.

```bash
bitbadges-cli simulate tx.json
bitbadges-cli simulate tx.json --creator bb1mysigner...
bitbadges-cli simulate tx.json --json
bitbadges-cli simulate tx.json --events           # dump full events array
echo '{"messages":[...]}' | bitbadges-cli simulate -
```

**Flags:**

| Flag | Description |
|---|---|
| `--json` | Output the structured `SimulateResult` as JSON |
| `--creator <address>` | Override the simulation context address (default: a placeholder) |
| `--events` | Dump the full raw chain events array (default: just the count) |
| `--testnet`, `--local`, `--url` | Network selection |

Requires `BITBADGES_API_KEY` (or per-network override) on mainnet/testnet. Local indexers usually accept any (or no) key.

User-level approval messages (`MsgUpdateUserApprovals`, `MsgSetIncomingApproval`, etc.) are explicitly refused — their semantics are state-change-on-existing-collection with set/append/delete variability and dry-running them isn't meaningful. Use `check` instead for those.

## preview

`bitbadges-cli preview` uploads a transaction to the indexer and prints a shareable `bitbadges.io/builder/preview?code=...` URL. Intended for handing a tx off to a non-CLI reviewer for visual inspection without giving them edit/submit rights. Lives 1 hour in the indexer's Redis cache.

```bash
bitbadges-cli preview tx.json
bitbadges-cli preview tx.json --json              # structured output
bitbadges-cli preview tx.json --frontend-url https://staging.bitbadges.io
echo '{"messages":[...]}' | bitbadges-cli preview -
```

**Flags:**

| Flag | Description |
|---|---|
| `--frontend-url <url>` | Override the bitbadges.io frontend base for the printed URL (default: `https://bitbadges.io`) |
| `--json` | Output the structured upload result as JSON instead of just the URL |
| `--testnet`, `--local`, `--url` | Network selection — controls which indexer hosts the preview |

The endpoint is intentionally open (no API key required); the unguessable code in the URL is the secret.

## See also

- [Build Commands](build-commands.md) — produce the JSON inputs you'd pass to these analysis verbs
- [Tool Commands](tool-commands.md) — fine-grained MCP tool surface for incremental construction
- [Deploy Commands](deploy-commands.md) — the broadcast side

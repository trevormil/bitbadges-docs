# Builder Commands

The `bitbadges-cli builder` command group is the umbrella for every non-template builder subcommand: the fine-grained tool registry, the review / verify / simulate / validate check family, the preview uploader, the environment doctor, the persisted session store, and the static resource registry.

If you are looking for flag-based template generators (`vault`, `nft`, `subscription`, …), see [Builder Templates](builder-templates.md). Everything else lives here.

All commands are also available via `bitbadgeschaind builder …` — the chain binary forwards `builder` subcommands to the Node.js CLI in-process, so there is no functional difference between `bitbadges-cli builder review tx.json` and `bitbadgeschaind builder review tx.json`.

## Overview

| Command | Purpose |
|---------|---------|
| [`builder tools list`](#builder-tools-list) | List every builder tool with its JSON schema |
| [`builder tools call <tool>`](#builder-tools-call) | Invoke a builder tool by name |
| [`builder review <input>`](#builder-review) | Run `reviewCollection()` over a tx or collection |
| [`builder verify <input>`](#builder-verify) | Aggregate validate + review + metadata coverage |
| [`builder validate <input>`](#builder-validate) | Offline structural validation only |
| [`builder simulate <input>`](#builder-simulate) | Dry-run a tx against the BitBadges API simulate endpoint |
| [`builder explain <input>`](#builder-explain) | Plain-English explanation of a tx or collection |
| [`builder preview <input>`](#builder-preview) | Upload a tx and print a shareable `bitbadges.io` preview URL |
| [`builder doctor`](#builder-doctor) | Environment + session health check |
| [`builder session list / show / reset`](#builder-session) | Inspect, dump, or delete persisted builder sessions |
| [`builder resources list / read`](#builder-resources) | Browse the static resource registry (recipes, skills, docs, …) |

Every command that takes an `<input>` accepts the same five shapes:

- A file path (`tx.json`)
- A `@file.json` path token
- Inline JSON (`'{"messages":[...]}'`)
- A numeric collection ID (for `review` and `explain` only — fetched from the API)
- `-` to read from stdin

## builder tools

The `tools` subcommand is the in-process twin of the BitBadges Builder MCP stdio server. Every tool the `bitbadges-builder-mcp` bin exposes to Claude Desktop is reachable here as a plain function call — no subprocess, no MCP protocol round-trip.

### builder tools list

List every builder tool with its full JSON schema.

```bash
bitbadges-cli builder tools list                  # full schemas, as JSON
bitbadges-cli builder tools list --names          # just the tool names, one per line
```

### builder tools call

Invoke a builder tool by name. Arguments come from `--args` (inline JSON) or `--args-file` (path to a JSON file).

```bash
bitbadges-cli builder tools call get_current_timestamp
bitbadges-cli builder tools call get_skill_instructions --args '{"skill":"smart-token"}'
bitbadges-cli builder tools call set_collection_metadata \
  --args-file ./metadata.json \
  --session demo
```

**Options:**

| Flag | Description |
|------|-------------|
| `--args <json>` | Tool arguments as an inline JSON string |
| `--args-file <path>` | Tool arguments read from a JSON file |
| `--session <id>` | Session id for stateful tools. Persisted to `~/.bitbadges/sessions/<id>.json`. Defaults to `--args.sessionId`, or the built-in default session. |
| `--raw` | Print the structured result instead of the formatted text block |

Stateful tools (`set_*`, `add_*`, `remove_*`, `get_transaction`, …) read from and write to the named session. Sessions survive across invocations, so agents can compose a collection across many CLI calls:

```bash
SESSION=demo
bitbadges-cli builder tools call set_standards --session $SESSION --args '{"standards":["SmartToken"]}'
bitbadges-cli builder tools call set_collection_metadata --session $SESSION --args @meta.json
bitbadges-cli builder tools call add_approval --session $SESSION --args @approval.json
bitbadges-cli builder tools call get_transaction --session $SESSION  # emit final JSON
```

Unknown tool names exit 1 and print the available tool list on stderr.

## builder review

Top-level alias for `sdk review`. Runs `reviewCollection()` over a transaction, collection, or on-chain collection ID and prints the grouped findings.

```bash
bitbadges-cli builder review tx.json
bitbadges-cli builder review collection.json --strict
bitbadges-cli builder review 42 --testnet               # fetch by collection id
echo '{"messages":[...]}' | bitbadges-cli builder review -
```

`reviewCollection()` composes three check families — `audit` (permission / supply / centralization), `standards` (protocol conformance), and `ux` (the deterministic UX checks ported from the frontend review sidebar) — into a single `Finding[]`. The CLI, the MCP `review_collection` tool, the indexer validation gate, and the frontend review sidebar all dispatch through this exact same entry point.

**Options:**

| Flag | Description |
|------|-------------|
| `--json` | Output the full `ReviewResult` as JSON |
| `--strict` | Exit 1 on warnings. Critical findings always exit 2. |
| `--output-file <path>` | Write the rendered output to a file instead of stdout |
| `--network <mainnet\|testnet\|local>` | Target network for numeric-id fetches |
| `--testnet` / `--local` / `--url <url>` | Shorthands for `--network` and base URL overrides |

## builder verify

One-stop diligence command. Runs `validate` + `review` + metadata coverage scan in a single pass and renders all three sections with the same terminal renderer the templates use. Saves agents 3+ separate calls per build.

```bash
bitbadges-cli builder verify tx.json
bitbadges-cli builder verify tx.json --json             # single structured payload
bitbadges-cli builder verify tx.json --strict           # exit 1 on any warnings
bitbadges-cli builder verify tx.json --no-metadata      # skip the metadata section
```

For transactions whose first collection message isn't at `messages[0]` (e.g. `[MsgTransferTokens, MsgCreateCollection]`, emitted by `add_transfer` + post-fact create), `verify` finds the collection message by walking the array. The `review` and metadata sections only run when a collection message is present — for user-level approvals, transfers, or dynamic store messages, only `validate` + `simulate` are useful signal and `review` stays silent.

**Options:**

| Flag | Description |
|------|-------------|
| `--json` | Output a structured aggregate JSON instead of rendered sections |
| `--strict` | Exit 1 on warnings; criticals always exit 2 |
| `--no-validate` | Skip the structural validation section |
| `--no-review` | Skip the design review section |
| `--no-metadata` | Skip the metadata coverage section |
| `--output-file <path>` | Write the rendered sections to a file instead of stdout |

Exit codes: `0` clean, `1` when `--strict` and warnings are present, `2` when there are critical findings or validation errors.

## builder validate

Offline structural validation only — no API calls, no design review, no metadata scan. Checks JSON structure, uint ranges, approval criteria shape, and every other invariant that can be verified statically.

```bash
bitbadges-cli builder validate tx.json
bitbadges-cli builder validate tx.json --json
```

Use `validate` when you want the fastest possible pre-commit gate (no network, no API key), and `verify` when you want the full diligence report.

## builder simulate

Dry-run a built tx against the BitBadges API `/api/v0/simulate` endpoint. Returns gas estimates and per-address net balance changes. Requires an API key — set `BITBADGES_API_KEY`, run `bitbadges-cli config set apiKey <key>`, or target a key-less local indexer with `--network local`.

```bash
bitbadges-cli builder simulate tx.json
bitbadges-cli builder simulate tx.json --json
bitbadges-cli builder simulate tx.json --events              # include raw chain events
bitbadges-cli builder simulate tx.json --creator bb1...      # override the sim context address
```

**Options:**

| Flag | Description |
|------|-------------|
| `--json` | Output the structured `SimulateResult` as JSON instead of a rendered section |
| `--creator <address>` | Override the simulation context address (default: `bb1simulation`) |
| `--events` | Dump the full raw chain events array in the rendered output (default: just the count) |
| `--network` / `--testnet` / `--local` / `--url` | Network targeting |

User-level approval messages (`MsgUpdateUserApprovals`, `MsgSetIncomingApproval`, `MsgSetOutgoingApproval`, `MsgDeleteIncomingApproval`, `MsgDeleteOutgoingApproval`, `MsgPurgeApprovals`) are **not simulable** — they describe a state mutation on an existing user approval list, not a self-contained tx. Use `builder validate` + `builder review` on those instead.

## builder explain

Human-readable explanation of a transaction body or a raw collection. Auto-detects which shape was passed: a `MsgCreateCollection` / `MsgUpdateCollection` / `MsgUniversalUpdateCollection` tx goes through `interpretTransaction()`, a raw collection goes through `interpretCollection()`. Numeric input is fetched from the indexer as a collection id.

```bash
bitbadges-cli builder explain tx.json
bitbadges-cli builder explain 42 --testnet
bitbadges-cli builder explain tx.json --output-file summary.txt
```

## builder preview

Upload a built tx to the indexer and print a shareable `bitbadges.io` preview URL. Lets agents and humans hand off a tx to a non-CLI reviewer for visual inspection without giving them edit or submit rights.

```bash
bitbadges-cli builder preview tx.json
bitbadges-cli builder preview tx.json --json
bitbadges-cli builder preview tx.json --frontend-url https://testnet.bitbadges.io
```

The endpoint (`/api/v0/builder/preview`) is open — no API key required. The unguessable code baked into the returned URL is the secret. Uploaded previews live in the indexer's Redis cache for one hour.

**Options:**

| Flag | Description |
|------|-------------|
| `--json` | Output the structured upload result (code, URL, expiry) as JSON |
| `--frontend-url <url>` | Override the `bitbadges.io` base for the printed URL (default: `https://bitbadges.io`) |
| `--network` / `--testnet` / `--local` / `--url` | Indexer target for the upload |

## builder doctor

One-shot environment + session health check. Runs six independent probes:

1. **Node version** — must be ≥ 18
2. **SDK package + version** — loaded from `bitbadgesjs-sdk`'s own `package.json`
3. **CLI config file** — `~/.bitbadges/config.json`
4. **API key reachability** — pings `/api/v0/simulate` with a no-op request
5. **MCP stdio bin presence** — smoke launch of the `bitbadges-builder-mcp` entry
6. **Persisted sessions** — every file under `~/.bitbadges/sessions/` parses

Each probe reports `PASS` / `FAIL` / `WARN` / `SKIP`. The command exits non-zero only if any probe is a hard `FAIL`; warnings and skips are informational.

```bash
bitbadges-cli builder doctor
bitbadges-cli builder doctor --json
bitbadges-cli builder doctor --with-preview   # adds a preview round-trip probe
```

`--with-preview` adds a seventh probe: builds a minimal tx, posts it to `/api/v0/builder/preview`, fetches it back by code, and asserts the round trip is byte-equivalent. Catches indexer, Redis, and preview-route regressions.

## builder session

Persisted builder sessions live at `~/.bitbadges/sessions/<id>.json`. They are the storage backend for the stateful `builder tools call` flow and the in-process MCP session dispatcher.

```bash
bitbadges-cli builder session list          # list every persisted session id
bitbadges-cli builder session show demo     # dump a session as JSON
bitbadges-cli builder session reset demo    # delete a session file
```

Sessions are plain JSON — you can inspect or edit them by hand if needed. Corrupted session files are surfaced by `builder doctor` on the next run.

## builder resources

The resource registry is the static half of the builder surface: the token registry, collection recipes, skill instructions, bundled docs, error patterns, and every other content resource the MCP server exposes via `bitbadges://` URIs.

```bash
bitbadges-cli builder resources list                       # full metadata, as JSON
bitbadges-cli builder resources list --uris                # URIs only, one per line
bitbadges-cli builder resources read bitbadges://skills/all
bitbadges-cli builder resources read bitbadges://recipes/all
```

Every resource URI exposed by the MCP server is readable through this command. Agents that want to pre-load skill instructions or recipe catalogues into a prompt can stream them directly from the CLI instead of spawning the MCP stdio bin.

## See Also

- [Builder Templates](builder-templates.md) — the `bitbadges-cli builder templates *` tree for flag-based template generators
- [SDK Commands](sdk-commands.md) — address, alias, lookup, and docs utilities under `bitbadges-cli sdk`
- [Builder Tools Reference](../ai-agents/builder-tools.md) — the full tool registry reference, including input schemas and workflows
- [CLI for AI Agents](for-ai-agents.md) — end-to-end agent workflows and automation patterns

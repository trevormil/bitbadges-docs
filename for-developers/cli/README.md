# CLI & Chain Binary

The BitBadges CLI is the fastest way to interact with the BitBadges blockchain. A single install gives you everything: the chain binary, the SDK CLI, and 104+ API routes — all from your terminal.

## One binary, one flat surface

There is exactly one canonical binary, `bitbadgeschaind`, shipped with a friendly two-letter alias `bb` via `install.bitbadges.io`. Use `bb` everywhere. The previous `bb cli <subcmd>` infix is gone — every SDK CLI verb is a first-class top-level command on `bb`:

```bash
bb build vault ...            # SDK builder (was: bb cli build vault)
bb auctions place-bid 42 ...  # SDK standards verb (was: bb cli auctions place-bid)
bb api tokens get-collection 1
bb tx tokenization create-collection ./col.json --from mykey   # chain native
bb query bank balances bb1abc...                                # chain native
bb auth login --browser --address bb1...
```

The flat surface comes with grouped help. Run `bb --help` and you see seven groups:

- **Chain node (Cosmos SDK)** — `start`, `init`, `status`, `version`, `tx`, `query`, `keys`, `sign-arbitrary`, `genesis`, `debug`, `config`, `prune`, `snapshots`, `comet` (the BitBadges chain node — distinct from the SDK indexer)
- **BitBadges SDK — Build & Deploy** — `build`, `check`, `explain`, `simulate`, `preview`, `deploy`
- **BitBadges SDK — Standards** — the 12 standards (`auctions`, `bounties`, `crowdfunds`, `credit-tokens`, `dynamic-stores`, `intents`, `nfts`, `pay-requests`, `prediction-markets`, `products`, `smart-tokens`, `subscriptions`)
- **BitBadges SDK — Indexer & Auth** — `api`, `auth`, `account`
- **BitBadges SDK — Swap / DEX** — `swap`, `pools`, `pairs`, `price`
- **BitBadges SDK — Dev** — `dev` (per-field MCP tools, resources, docs browser, skills, pubkey utilities)
- **Local state** — `settings`, `session`, `burner`, `doctor`, `completion`

The groups exist so you can tell at a glance which surface owns a given verb — chain natives on top, SDK CLI below, local state at the bottom.

## Install (One-Liner)

```bash
curl -fsSL https://install.bitbadges.io | sh
```

This installs the chain binary as `bitbadgeschaind` (with the `bb` alias) and the standalone SDK CLI (`bitbadges-cli`). Every example in these docs uses `bb`; the standalone `bitbadges-cli` binary still works for the SDK-only subset if you don't need the chain binary.

Verify your installation:

```bash
bb version
bb doctor
```

## Quick examples

```bash
# 1. Native chain query
bb query bank balances bb1abc... --output json

# 2. Indexer call
bb api tokens get-collection 1 --output json

# 3. Authenticated session — wallet-agnostic, headless-friendly
MSG=$(bb auth challenge --address bb1... | jq -r .data.message)
SIG_JSON=$(bb sign-arbitrary mykey "$MSG")
bb auth login \
  --address    "$(echo "$SIG_JSON" | jq -r .address)" \
  --signature  "$(echo "$SIG_JSON" | jq -r .signature)" \
  --public-key "$(echo "$SIG_JSON" | jq -r .pubKey)" \
  --message    "$MSG"

# 4. Discover the full command tree as JSON — for AI agents
bb --help-json | jq '.commands[] | .name'
```

## What You Can Do

| Capability | Command | Example |
|-----------|---------|---------|
| **Create a collection with no wallet setup** | `bb deploy --burner` | `bb build subscription … \| bb deploy --burner --msg-stdin --manager bb1… --local` |
| **Sign with a browser wallet (Keplr / MetaMask)** | `bb auth login --browser` | `bb auth login --browser --address bb1...` |
| **Broadcast via your browser wallet** | `bb deploy --browser` | `bb deploy --browser --msg-file col.json --manager bb1...` |
| **Build + broadcast in one step** | `bb build … --deploy-with-browser` | `bb build vault --name … --deploy-with-browser` |
| **Sign now, broadcast later** | `bb deploy --browser --sign-only` | `bb deploy --browser --sign-only --msg-file col.json --manager bb1...` |
| **Get signable payload for a custom signer (ethers/viem/HSM)** | `bb deploy --gen-payload` | `bb build vault … \| bb deploy --gen-payload --from bb1... --with-evm-tx` |
| **Confirm a tx landed on chain** | `bb tx wait` | `bb tx wait $TXHASH --mainnet --timeout 60` |
| **Query any collection** | `bb api tokens get-collection` | `bb api tokens get-collection 1` |
| **Review and audit tokens** | `bb check` | `bb check tx.json` |
| **Explain a tx or collection** | `bb explain` | `bb explain tx.json` |
| **Dry-run a transaction** | `bb simulate` | `bb simulate tx.json` |
| **Dry-run a deploy without spending** | `bb deploy --dry-run` | `bb deploy vault.json --burner --dry-run --manager bb1...` |
| **Share a tx for visual review** | `bb preview` | `bb preview tx.json` |
| **Browse 100+ API routes** | `bb api` | `bb api tokens --help` |
| **Search API routes by keyword** | `bb api --search` | `bb api --search swap` |
| **Inspect a route's schema** | `bb api ... --schema` | `bb api tokens get-collection --schema` |
| **Convert addresses** | `bb account convert` | `bb account convert 0x1234... --to bb1` |
| **Look up token info** | `bb account lookup` | `bb account lookup USDC` |
| **Browse docs** | `bb dev docs` | `bb dev docs learn/approvals` |
| **List builder skills** | `bb dev skills` | `bb dev skills smart-token` |
| **Health check** | `bb doctor` | `bb doctor --testnet` |
| **Create/sign transactions** | `bb tx` | `bb tx tokenization create-collection ./col.json --from mykey` |
| **Manage keys** | `bb keys` | `bb keys add mykey` |
| **Query on-chain state** | `bb query` | `bb query tokenization collection 1` |

## Deprecation runway — the old `bb cli` and standalone names still work

Every old form — `bb cli <subcmd>`, the per-utility top-level names that moved under `bb account` / `bb dev` / `bb settings`, the standalone `sign-with-browser` and `gen-tx-payload`, the per-standard `<standard> build` subcommands — is still accepted for **one release**. Running the old form prints a one-line deprecation banner to stderr:

```
[bb] DEPRECATED: `bb cli build vault` — use `bb build vault` instead.
```

Set `BB_QUIET=1` (or pass `--quiet`) to suppress the banner. The next release after the deprecation window hard-fails old forms and removes every alias. Update your scripts and agent prompts before then.

If you are reading the historical migration note for a specific verb, the deprecation banner is the authoritative source of the new name — it always points at the v2 short form.

## For AI Agents

The CLI is the recommended interface for AI agents and automation. It provides:

- **Structured JSON output** for every data-emitting command — easy to parse programmatically
- **Uniform output envelope** on stdout for every data-emitting command: `{ok, data, warnings, hint?, meta?, error}` — same shape across the surface, with hints populated on common failure modes. The envelope is the only output mode; `--format` / `--json` / `--human` flags are gone. Universal flags: `--condensed` (single-line) and `--output-file <path>`. `bb build` extends the envelope with a `meta` sidecar carrying validation, review, simulate, and resolved-metadata reports alongside the msg in `data`.
- **`--help-json`** flag that outputs the full command tree as structured JSON for LLM tool discovery
- **`--dry-run`** on both `simulate` and `deploy` for safe preview before broadcasting
- **`--quiet`** (or `BB_QUIET=1`) silences stderr commentary across every command — pipe-friendly by default; this is also the toggle that suppresses the deprecation banner during the migration window
- **`api --search` / `--schema`** for route discovery without grepping help text
- **`tx status` / `tx wait`** to confirm a tx landed on chain (Cosmos LCD + EVM RPC fall-through)
- **Targeted `hint:` on common errors** — auth-rejected, 401/403, deploy insufficient-funds, tx-wait timeout — designed to cut agent retry loops in half
- **Shell completion** for interactive use: `eval "$(bb completion)"`
- **Pipe-friendly** — accepts stdin (`-`), file paths (`@file.json`), and inline JSON

See [CLI for AI Agents](for-ai-agents.md) for end-to-end agent workflows.

For building token collections with AI assistants (Claude, Cursor, etc.), see the [BitBadges Builder Tools](../ai-agents/builder-tools.md) which provide 50+ tools on top of the CLI.

## Next Steps

- [Installation](installation.md) — all install methods and configuration
- [Build Commands](build-commands.md) — flag-based generators for vault, subscription, bounty, auction, and 14 other token types
- [Standards Commands](standards-commands.md) — consumer-side `list / show / status / <action>` for every standard (auctions, crowdfunds, payment-requests, intents, swap, ...)
- [Analysis Commands](analysis-commands.md) — `check`, `explain`, `simulate`, `preview`
- [Deploy Commands](deploy-commands.md) — ship a create-collection tx without bringing your own wallet (also covers `--wait-for-indexer` + `--with-keyring`)
- [Tx Commands](tx-commands.md) — confirm a broadcast tx committed (Cosmos + EVM hash support)
- [Tool Commands](tool-commands.md) — fine-grained MCP tools (`bb dev tools list` / `bb dev tools call`), persisted sessions, static resources
- [Utility Commands](utility-commands.md) — `bb dev docs`, `bb dev skills`, `bb account convert`, `bb account alias`, `bb account lookup`, `bb account gen-list-id`, `bb doctor`
- [API Commands](api-commands.md) — 104+ API routes from your terminal
- [Auth Commands](auth-commands.md) — wallet-agnostic SIWBB sessions for Full Access endpoints
- [Sign Bridge](sign-bridge.md) — sign with a browser wallet (Keplr/MetaMask) from the CLI; `bb deploy --gen-payload` for custom programmatic signers
- [Chain Commands](chain-commands.md) — keys, transactions, on-chain queries
- [CLI for AI Agents](for-ai-agents.md) — agent workflows and automation patterns

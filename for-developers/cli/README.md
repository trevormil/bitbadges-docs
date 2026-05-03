# CLI & Chain Binary

The BitBadges CLI is the fastest way to interact with the BitBadges blockchain. A single install gives you everything: the chain binary (`bitbadgeschaind`), the SDK CLI (`bitbadges-cli`), and 104+ API routes — all from your terminal.

## One binary, two surfaces

There is exactly one canonical binary: `bitbadgeschaind`. It exposes two surfaces:

| Surface | Form | What it covers |
|---|---|---|
| **Cosmos-native chain ops** | `bitbadgeschaind tx \| query \| keys ...` | Key management, transaction signing, broadcasting, on-chain reads. Same surface as any Cosmos SDK chain binary. |
| **BitBadges SDK CLI (forwarded)** | `bitbadgeschaind cli <subcmd>` | The full Node SDK CLI — API access, review/audit, docs browser, skills, address tools. Identical surface to `bitbadges-cli`, just routed through the chain binary. |

We ship a friendly alias, `bb`, via `install.bitbadges.io`. Use `bb` as the entry point in examples and scripts — it's just `bitbadgeschaind` under the hood, and the chain-vs-cli surface split is identical.

## Install (One-Liner)

```bash
curl -fsSL https://install.bitbadges.io | sh
```

This installs both:
- **`bitbadgeschaind`** (with the `bb` alias) — the chain binary (key management, signing, broadcasting, on-chain queries) AND the forwarder for the SDK CLI.
- **`bitbadges-cli`** — the standalone SDK CLI (API access, review/audit, docs browser, skills, address tools). Equivalent to `bb cli ...`.

Verify your installation:

```bash
bb version
bb cli doctor
```

## Quick examples

Four shapes worth knowing:

```bash
# 1. Native chain query — Cosmos SDK surface, no forwarding
bb query bank balances bb1abc... --output json

# 2. Forwarded SDK call — `bb cli` is shorthand for the Node SDK CLI
bb cli api tokens get-collection 1 --output json

# 3. Authenticated session — wallet-agnostic, headless-friendly
#    (full reference: for-developers/cli/auth-commands.md)
MSG=$(bb cli auth challenge --address bb1...)
SIG_JSON=$(bb sign-arbitrary mykey "$MSG")
bb cli auth login \
  --address    "$(echo "$SIG_JSON" | jq -r .address)" \
  --signature  "$(echo "$SIG_JSON" | jq -r .signature)" \
  --public-key "$(echo "$SIG_JSON" | jq -r .pubKey)" \
  --message    "$MSG"

# 4. Discover the full command tree as JSON — for AI agents
bb --help-json | jq '.commands[] | .name'
```

## How They Work Together

The chain binary delegates every `bitbadges-cli` subcommand to the Node.js CLI via a single `cli` forwarder. These are equivalent:

```bash
bb cli check tx.json
bitbadges-cli check tx.json

bb cli api tokens get-collection 1
bitbadges-cli api tokens get-collection 1
```

Use whichever you prefer. The chain binary adds key management, transaction signing, and on-chain queries on top of what the SDK CLI provides.

## What You Can Do

| Capability | Command | Example |
|-----------|---------|---------|
| **Create a collection with no wallet setup** | `bitbadges-cli deploy --burner` | `bitbadges-cli build subscription … \| bitbadges-cli deploy --burner --msg-stdin --manager bb1… --local` |
| **Sign with a browser wallet (Keplr / MetaMask)** | `bitbadges-cli auth login --browser` | `bitbadges-cli auth login --browser --address bb1...` |
| **Broadcast via your browser wallet** | `bitbadges-cli deploy --browser` | `bitbadges-cli deploy --browser --msg-file col.json --manager bb1...` |
| **Build + broadcast in one step** | `bitbadges-cli build … --deploy-with-browser` | `bitbadges-cli build vault --name … --deploy-with-browser` |
| **Sign now, broadcast later** | `bitbadges-cli deploy --browser --sign-only` | `bitbadges-cli deploy --browser --sign-only --msg-file col.json --manager bb1...` |
| **Get signable payload for a custom signer (ethers/viem/HSM)** | `bitbadges-cli gen-tx-payload` | `bitbadges-cli build vault … \| bitbadges-cli gen-tx-payload --from bb1... --with-evm-tx` |
| **Confirm a tx landed on chain** | `bitbadges-cli tx wait` | `bitbadges-cli tx wait $TXHASH --mainnet --timeout 60` |
| **Query any collection** | `bitbadges-cli api tokens get-collection` | `bitbadges-cli api tokens get-collection 1` |
| **Review and audit tokens** | `bitbadges-cli check` | `bitbadges-cli check tx.json` |
| **Explain a tx or collection** | `bitbadges-cli explain` | `bitbadges-cli explain tx.json` |
| **Dry-run a transaction** | `bitbadges-cli simulate` | `bitbadges-cli simulate tx.json` |
| **Dry-run a deploy without spending** | `bitbadges-cli deploy --dry-run` | `bitbadges-cli deploy vault.json --burner --dry-run --manager bb1...` |
| **Share a tx for visual review** | `bitbadges-cli preview` | `bitbadges-cli preview tx.json` |
| **Browse 100+ API routes** | `bitbadges-cli api` | `bitbadges-cli api tokens --help` |
| **Search API routes by keyword** | `bitbadges-cli api --search` | `bitbadges-cli api --search swap` |
| **Inspect a route's schema** | `bitbadges-cli api ... --schema` | `bitbadges-cli api tokens get-collection --schema` |
| **Convert addresses** | `bitbadges-cli address convert` | `bitbadges-cli address convert 0x1234... --to bb1` |
| **Look up token info** | `bitbadges-cli lookup` | `bitbadges-cli lookup USDC` |
| **Browse docs** | `bitbadges-cli docs` | `bitbadges-cli docs learn/approvals` |
| **List builder skills** | `bitbadges-cli skills` | `bitbadges-cli skills smart-token` |
| **Health check** | `bitbadges-cli doctor` | `bitbadges-cli doctor --testnet` |
| **Create/sign transactions** | `bitbadgeschaind tx` | `bitbadgeschaind tx tokenization create-collection ./col.json --from mykey` |
| **Manage keys** | `bitbadgeschaind keys` | `bitbadgeschaind keys add mykey` |
| **Query on-chain state** | `bitbadgeschaind query` | `bitbadgeschaind query tokenization collection 1` |

## For AI Agents

The CLI is the recommended interface for AI agents and automation. It provides:

- **Structured JSON output** for every command — easy to parse programmatically
- **Uniform output envelope** on `--format json` for the agent-facing verbs (`tx`, `explain`, `api --search`): `{ok, data, warnings, hint, error}` — same shape across every command, with hints populated on common failure modes
- **`--help-json`** flag that outputs the full command tree as structured JSON for LLM tool discovery
- **`--dry-run`** on both `simulate` and `deploy` for safe preview before broadcasting
- **`--quiet`** (or `BB_QUIET=1`) silences stderr commentary across every command — pipe-friendly by default
- **`api --search` / `--schema`** for route discovery without grepping help text
- **`tx status` / `tx wait`** to confirm a tx landed on chain (Cosmos LCD + EVM RPC fall-through)
- **Targeted `hint:` on common errors** — auth-rejected, 401/403, deploy insufficient-funds, tx-wait timeout — designed to cut agent retry loops in half
- **Shell completion** for interactive use: `eval "$(bitbadges-cli completion)"`
- **Pipe-friendly** — accepts stdin (`-`), file paths (`@file.json`), and inline JSON

See [CLI for AI Agents](for-ai-agents.md) for end-to-end agent workflows.

For building token collections with AI assistants (Claude, Cursor, etc.), see the [BitBadges Builder Tools](../ai-agents/builder-tools.md) which provide 50+ tools on top of the CLI.

## Next Steps

- [Installation](installation.md) — all install methods and configuration
- [Build Commands](build-commands.md) — flag-based generators for vault, subscription, bounty, auction, and 14 other token types
- [Analysis Commands](analysis-commands.md) — `check`, `explain`, `simulate`, `preview`
- [Deploy Commands](deploy-commands.md) — ship a create-collection tx without bringing your own wallet
- [Tx Commands](tx-commands.md) — confirm a broadcast tx committed (Cosmos + EVM hash support)
- [Tool Commands](tool-commands.md) — fine-grained MCP tools (`tool` / `tools`), persisted sessions, static resources
- [Utility Commands](utility-commands.md) — `docs`, `skills`, `address`, `alias`, `lookup`, `gen-list-id`, `doctor`
- [API Commands](api-commands.md) — 104+ API routes from your terminal
- [Auth Commands](auth-commands.md) — wallet-agnostic Blockin sessions for Full Access endpoints
- [Sign Bridge](sign-bridge.md) — sign with a browser wallet (Keplr/MetaMask) from the CLI; `gen-tx-payload` for custom programmatic signers
- [Chain Commands](chain-commands.md) — keys, transactions, on-chain queries
- [CLI for AI Agents](for-ai-agents.md) — agent workflows and automation patterns

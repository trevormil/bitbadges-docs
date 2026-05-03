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
bb cli sdk status
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
bb cli sdk review tx.json
bitbadges-cli sdk review tx.json

bb cli api tokens get-collection 1
bitbadges-cli api tokens get-collection 1
```

Use whichever you prefer. The chain binary adds key management, transaction signing, and on-chain queries on top of what the SDK CLI provides.

## What You Can Do

| Capability | Command | Example |
|-----------|---------|---------|
| **Create a collection with no wallet setup** | `bitbadges-cli builder create-with-burner` | `bitbadges-cli builder templates subscription … \| bitbadges-cli builder create-with-burner --msg-stdin --manager bb1… --local` |
| **Query any collection** | `bitbadges-cli api tokens get-collection` | `bitbadges-cli api tokens get-collection 1` |
| **Review and audit tokens** | `bitbadges-cli sdk review` | `bitbadges-cli sdk review tx.json` |
| **Browse 104+ API routes** | `bitbadges-cli api` | `bitbadges-cli api tokens --help` |
| **Convert addresses** | `bitbadges-cli sdk address convert` | `bitbadges-cli sdk address convert 0x1234... --to bb1` |
| **Look up token info** | `bitbadges-cli sdk lookup-token` | `bitbadges-cli sdk lookup-token USDC` |
| **Browse docs** | `bitbadges-cli sdk docs` | `bitbadges-cli sdk docs learn/approvals` |
| **List builder skills** | `bitbadges-cli sdk skills` | `bitbadges-cli sdk skills smart-token` |
| **Create/sign transactions** | `bitbadgeschaind tx` | `bitbadgeschaind tx tokenization create-collection ./col.json --from mykey` |
| **Manage keys** | `bitbadgeschaind keys` | `bitbadgeschaind keys add mykey` |
| **Query on-chain state** | `bitbadgeschaind query` | `bitbadgeschaind query tokenization collection 1` |

## For AI Agents

The CLI is the recommended interface for AI agents and automation. It provides:

- **Structured JSON output** for every command — easy to parse programmatically
- **`--help-json`** flag that outputs the full command tree as structured JSON for LLM tool discovery
- **`--dry-run`** for safe simulation before broadcasting
- **Shell completion** for interactive use: `eval "$(bitbadges-cli completion)"`
- **Pipe-friendly** — accepts stdin (`-`), file paths (`@file.json`), and inline JSON

See [CLI for AI Agents](for-ai-agents.md) for end-to-end agent workflows.

For building token collections with AI assistants (Claude, Cursor, etc.), see the [BitBadges Builder Tools](../ai-agents/builder-tools.md) which provide 50+ tools on top of the CLI.

## Next Steps

- [Installation](installation.md) — all install methods and configuration
- [SDK Commands](sdk-commands.md) — review, interpret, address tools, docs, skills
- [API Commands](api-commands.md) — 104+ API routes from your terminal
- [Chain Commands](chain-commands.md) — keys, transactions, on-chain queries
- [Builder Templates](builder-templates.md) — flag-based generators for vault, subscription, bounty, auction, and 14 other token types
- [Create With Burner](create-with-burner.md) — ship a create-collection tx without bringing your own wallet
- [Builder Commands](builder-commands.md) — tools, review, verify, simulate, preview, doctor, session, resources
- [CLI for AI Agents](for-ai-agents.md) — agent workflows and automation patterns

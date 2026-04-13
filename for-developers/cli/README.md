# CLI & Chain Binary

The BitBadges CLI is the fastest way to interact with the BitBadges blockchain. A single install gives you everything: the chain binary (`bitbadgeschaind`), the SDK CLI (`bitbadges-cli`), and 104+ API routes — all from your terminal.

## Install (One-Liner)

```bash
curl -fsSL https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/install.sh | sh
```

This installs both:
- **`bitbadgeschaind`** — the chain binary (key management, signing, broadcasting, on-chain queries)
- **`bitbadges-cli`** — the SDK CLI (API access, review/audit, docs browser, skills, address tools)

Verify your installation:

```bash
bitbadgeschaind version
bitbadges-cli sdk status
```

## How They Work Together

The chain binary delegates `sdk` and `api` subcommands to the Node.js CLI. These are equivalent:

```bash
bitbadgeschaind sdk review tx.json
bitbadges-cli sdk review tx.json

bitbadgeschaind api tokens get-collection 1
bitbadges-cli api tokens get-collection 1
```

Use whichever you prefer. The chain binary adds key management, transaction signing, and on-chain queries on top of what the SDK CLI provides.

## What You Can Do

| Capability | Command | Example |
|-----------|---------|---------|
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
- [CLI for AI Agents](for-ai-agents.md) — agent workflows and automation patterns

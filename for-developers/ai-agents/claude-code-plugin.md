# 🧩 Claude Code Plugin

The BitBadges Claude Code plugin is a convenience layer on top of the BitBadges chain binary + CLI for Claude Code users specifically. It auto-wires the `bitbadges-builder` MCP server and ships ~29 curated skills covering every token type plus operational workflows.

## Prerequisites

The chain binary + CLI install is the canonical entrypoint for everything BitBadges:

```sh
curl -fsSL https://install.bitbadges.io | sh
bitbadges-cli config set apiKey YOUR_KEY
```

This installs `bitbadgeschaind` and `bitbadges-cli` (which includes the `bitbadges-builder` MCP bin). The plugin uses these — without them, the plugin falls back to running the MCP via `npx -y -p bitbadges bitbadges-builder`, which works but is slower and less reliable than a globally installed CLI.

## Install the plugin

After the prerequisites above:

```
/plugin marketplace add BitBadges/bitbadges-plugin
/plugin install bitbadges
```

Run `/bitbadges:setup` once to confirm everything's wired and `/bitbadges:status` whenever you want a health check.

## What you get

### MCP server (auto-wired)

The plugin's `.mcp.json` registers `bitbadges-builder` automatically — no `claude mcp add` step. Under the hood, the plugin runs:

```
npx -y -p bitbadges bitbadges-builder
```

`npx` resolves the locally installed `bitbadges` package from the prerequisites step above. If you skipped that install, npx falls back to fetching the package from the npm registry on first call — slower and less reliable, but it works.

### Skills (~29 total)

**Token creation (22 skills, generated from the SDK):**
Smart Token, Fungible Token, NFT Collection, Subscription, Liquidity Pools, BB-402 Token-Gated Access, Burnable, Multi-Sig / Voting, Payment Protocol, Tradable NFTs, Credit Token, Auto-Mint, Address List, Quest, Prediction Market, Bounty, Crowdfund, Auction, Products, Custom 2FA, Minting, Transferability & Update Rules.

Each skill is a thin routing wrapper. The full instructions live in the SDK and are loaded on demand via the `get_skill_instructions` MCP tool.

**Operational (7 hand-written skills):**

| Skill | Purpose |
|---|---|
| `review` | Audit a transaction file or live collection for correctness, standards compliance, foot-guns. |
| `simulate` | Dry-run a transaction. Returns events + balance diffs. Always before broadcast. |
| `explain` | Plain-English description, audience-aware (user / developer / auditor). |
| `query` | The 104+ API routes — discovery first via `--help-json`, then call. |
| `address` | All six address operations (cosmos↔EVM, IBC backing, wrapper, mint-escrow, alias). |
| `claim` | Build or audit a claim (whitelist / password / codes / open / token-gated). |
| `broadcast` | Sign + broadcast. Hard-rails — dry-run by default, explicit confirmation for live. |

### Slash commands

| Command | What it does |
|---|---|
| `/bitbadges:setup` | One-time prereq check + API key wiring. Reads `~/.bitbadges/config.json` first (CLI config is canonical) before prompting. |
| `/bitbadges:status` | Quick health check — chain binary, CLI version, API connectivity, MCP reachability, network. |

### Subagent

`bitbadges-builder` — a focused builder loop primed to reach for MCP tools first, fall back to CLI, only touch the chain binary for live broadcasts. Use when you want isolation from the main conversation: "spawn the bitbadges-builder agent and have it build me a smart token end to end".

### SessionStart pre-warm hook

The plugin pre-warms the npx cache for the `bitbadges` package on every session start. First MCP-tool call is fast instead of paying 5–15s of npm download.

## API key

The plugin reads `~/.bitbadges/config.json` if you've already configured the BitBadges CLI. Otherwise `/bitbadges:setup` prompts and writes via `bitbadges-cli config set apiKey <KEY>` so both the CLI and the plugin pick it up.

Get an API key at [bitbadges.io/developer](https://bitbadges.io/developer).

## Migration from manual MCP setup

If you previously ran:

```
claude mcp add bitbadges-builder -- npx -y -p bitbadges bitbadges-builder
```

Remove the user-scope entry before installing the plugin to avoid duplicate MCP servers:

```
claude mcp remove bitbadges-builder
```

`/bitbadges:setup` detects duplicates and offers cleanup.

## When you don't need the plugin

The plugin is for Claude Code users specifically. For other harnesses, BitBadges still has full coverage via the same MCP server and the existing skill docs:

- **Cursor / Claude Desktop / other MCP clients** → set up the `bitbadges-builder` MCP server (`claude mcp add bitbadges-builder -- npx -y -p bitbadges bitbadges-builder` for Claude Desktop, or the equivalent in your client). The MCP exposes `get_skill_instructions(<id>)` for on-demand loading — same path the plugin uses.
- **Generic LLMs / shell scripts / CI** → use the [CLI](../cli/) directly. For skill instructions, browse the rendered [skill pages](../../x-tokenization/examples/skills/) or call `bitbadges-cli sdk skills <id>`.
- **TypeScript/JavaScript devs** → `npm install bitbadges` and use the [SDK](../bitbadges-sdk/README.md) directly.

The CLI is the universal base layer; the plugin is just a Claude-Code-specific veneer. Skill instruction content is rendered in one place — [`x-tokenization/examples/skills/`](../../x-tokenization/examples/skills/) — and consumed everywhere else (plugin, MCP, CLI) by reference.

## Source

- Plugin repo: [BitBadges/bitbadges-plugin](https://github.com/BitBadges/bitbadges-plugin)
- MCP server source: [bitbadgesjs/packages/bitbadgesjs-sdk/src/builder](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/builder)
- CLI source: [bitbadgesjs/packages/bitbadgesjs-sdk/src/cli](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/cli)
- Skills source of truth: [bitbadgesjs-sdk/src/builder/resources/skillInstructions.ts](https://github.com/BitBadges/bitbadgesjs/blob/main/packages/bitbadgesjs-sdk/src/builder/resources/skillInstructions.ts)

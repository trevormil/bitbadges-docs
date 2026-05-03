# CLI for AI Agents

The BitBadges CLI is the recommended interface for AI agents, bots, and automated systems. Start by installing the chain binary + CLI via `curl -fsSL https://install.bitbadges.io | sh` — that's the canonical entrypoint for every harness covered below.

The CLI provides structured JSON output, pipe-friendly input, and LLM-consumable command discovery — everything an agent needs without writing TypeScript.

> **Claude Code users:** after the CLI install above, you can optionally layer the [BitBadges plugin](../ai-agents/claude-code-plugin.md) on top for auto-wired MCP and 8 workflow skills that route Claude to the right CLI / MCP / docs surface (`/plugin install bitbadges`). The plugin is a convenience layer; everything on this page works with or without it.

## Why CLI for Agents?

- **Structured JSON output** — every command returns parseable JSON
- **`--help-json`** — outputs the full command tree as structured JSON, perfect for LLM tool discovery
- **`--dry-run`** — simulate any API call without side effects
- **Pipe-friendly** — accepts stdin (`-`), file paths (`@file.json`), and inline JSON
- **Self-documenting** — `bitbadges-cli docs all` dumps the entire documentation
- **No build step** — no TypeScript compilation, no bundling, just install and run

## Quick Setup for Agents

```bash
# 1. Install everything
curl -fsSL https://install.bitbadges.io | sh

# 2. Configure API key
bitbadges-cli config set apiKey YOUR_KEY

# 3. Verify
bitbadges-cli doctor
```

> **Heads up — API key vs. session.** The API key is required on every indexer call but is the *app* scope. Anything that mutates an account, manages keys, or publishes signed data also needs a *user* scope: a session cookie obtained via [`auth login`](auth-commands.md). The CLI is wallet-agnostic — pair it with `bitbadgeschaind sign-arbitrary` for fully headless Cosmos signing, or paste in a signature from any external wallet.

## Agent Workflow: Query, Review, Transact

### Step 1: Discover available commands

```bash
# Get full command tree as JSON (for LLM tool discovery)
bitbadges-cli --help-json

# List all API route groups
bitbadges-cli api --help

# List routes in a specific group
bitbadges-cli api tokens --help
```

### Step 2: Query data

```bash
# Get a collection
bitbadges-cli api tokens get-collection 1

# Check a balance
bitbadges-cli api tokens get-balance --body '{"collectionId":"1","address":"bb1..."}'

# Look up an account
bitbadges-cli api accounts get-account --body '{"address":"bb1..."}'

# Search
bitbadges-cli api misc search --body '{"query":"my-token"}'
```

### Step 3: Review and audit

```bash
# Review a transaction before broadcasting
bitbadges-cli check tx.json

# Get human-readable explanation
bitbadges-cli explain tx.json

# Review a live collection
bitbadges-cli check 42 --testnet

# Interpret what a collection does
bitbadges-cli explain 42
```

### Step 4: Browse docs and skills

```bash
# Dump full docs for context
bitbadges-cli docs all

# Browse a specific topic
bitbadges-cli docs learn/approvals

# List available builder skills
bitbadges-cli skills

# Get instructions for building a specific token type
bitbadges-cli skills smart-token
```

### Step 5: Sign and broadcast (with chain binary)

```bash
# Create a key for the agent
bitbadgeschaind keys add agent-wallet

# Sign and broadcast a transaction
bitbadgeschaind tx tokenization create-collection ./collection.json \
  --from agent-wallet --chain-id bitbadges-1 \
  --node https://lcd.bitbadges.io:443 \
  --gas auto --gas-adjustment 1.5 --fees 10000ubadge

# Or broadcast via the API
bitbadges-cli api tx broadcast-tx --body @signed-tx.json
```

### Step 6: Authenticate for `Full Access` API routes

Most read-only API routes work with just the API key, but anything that mutates an account or publishes signed data needs a session cookie. The CLI ships a wallet-agnostic three-step flow that pairs cleanly with the chain binary's offline signer:

```bash
# 1. Fetch a challenge (saves the nonce cookie locally for step 3)
MSG=$(bitbadges-cli auth challenge --address $(bitbadgeschaind keys show agent-wallet -a))

# 2. Sign offline with the chain binary
SIG_JSON=$(bitbadgeschaind sign-arbitrary agent-wallet "$MSG")

# 3. Post the signature — stores the session under ~/.bitbadges/auth.json
bitbadges-cli auth login \
  --address    "$(echo "$SIG_JSON" | jq -r .address)" \
  --signature  "$(echo "$SIG_JSON" | jq -r .signature)" \
  --public-key "$(echo "$SIG_JSON" | jq -r .pubKey)" \
  --message    "$MSG"

# 4. Make Full Access requests by adding --with-session
bitbadges-cli api accounts get-account --body '{"address":"bb1..."}' --with-session
```

Multi-account, multi-network, with `--2fa` support. Full reference: [Auth Commands](auth-commands.md).

## Combining with BitBadges Builder Tools

For AI assistants that speak MCP (Claude, Cursor, etc.), the [BitBadges Builder Tools](../ai-agents/builder-tools.md) provide 50+ higher-level tools for building token collections. The CLI and builder tools are complementary:

| Use Case | Tool |
|----------|------|
| Query data, browse docs, review transactions | **CLI** (`bitbadges-cli`) |
| Build token collections with guided workflows | **BitBadges Builder Tools** |
| Sign and broadcast transactions | **Chain binary** (`bitbadgeschaind`) or **SDK Signing Client** |
| Key management | **Chain binary** (`bitbadgeschaind`) |

## Example: Fully Automated Agent Script

```bash
#!/bin/bash
# Agent that checks a balance and conditionally transfers tokens

COLLECTION_ID=1
ADDRESS="bb1..."
THRESHOLD=10

# Query current balance
BALANCE=$(bitbadges-cli api tokens get-balance \
  --body "{\"collectionId\":\"$COLLECTION_ID\",\"address\":\"$ADDRESS\"}" \
  --condensed)

# Parse and decide
AMOUNT=$(echo "$BALANCE" | jq -r '.balances[0].amount // "0"')

if [ "$AMOUNT" -lt "$THRESHOLD" ]; then
  echo "Balance $AMOUNT below threshold $THRESHOLD, minting..."
  
  # Review the transaction first
  bitbadges-cli check ./mint-tx.json --testnet
  
  # If review passes, broadcast
  bitbadgeschaind tx tokenization transfer-tokens ./mint-tx.json \
    --from agent-wallet --chain-id bitbadges-2 \
    --node https://lcd-testnet.bitbadges.io:443 \
    --gas auto --fees 10000ubadge
fi
```

## Further Reading

- [Bot Examples](../ai-agents/bot-examples.md) — TypeScript bot patterns
- [Agent Spending Authorization](../ai-agents/agent-spending-authorization.md) — on-chain spending limits for agents
- [BitBadges Builder Tools](../ai-agents/builder-tools.md) — 50+ tools for AI assistants
- [Claims for Agents](../ai-agents/claims-for-agents.md) — automated minting via claims

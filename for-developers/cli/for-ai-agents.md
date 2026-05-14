# CLI for AI Agents

The BitBadges CLI is the recommended interface for AI agents, bots, and automated systems. Start by installing the chain binary + CLI via `curl -fsSL https://install.bitbadges.io | sh` — that's the canonical entrypoint for every harness covered below.

The CLI provides structured JSON output, pipe-friendly input, and LLM-consumable command discovery — everything an agent needs without writing TypeScript.

> **Claude Code users:** after the CLI install above, you can optionally layer the [BitBadges plugin](../ai-agents/claude-code-plugin.md) on top for auto-wired MCP and 8 workflow skills that route Claude to the right CLI / MCP / docs surface (`/plugin install bitbadges`). The plugin is a convenience layer; everything on this page works with or without it.

## Why CLI for Agents?

- **Structured JSON output** — every command returns parseable JSON
- **`--help-json`** — outputs the full command tree as structured JSON, perfect for LLM tool discovery
- **`--dry-run`** — simulate any API call without side effects
- **Pipe-friendly** — accepts stdin (`-`), file paths (`@file.json`), and inline JSON
- **Self-documenting** — `bb dev docs all` dumps the entire documentation
- **No build step** — no TypeScript compilation, no bundling, just install and run

## Quick Setup for Agents

```bash
# 1. Install everything
curl -fsSL https://install.bitbadges.io | sh

# 2. Configure API key
bb settings set apiKey YOUR_KEY

# 3. Verify
bb doctor
```

> **Heads up — API key vs. session.** The API key is required on every indexer call but is the *app* scope. Anything that mutates an account, manages keys, or publishes signed data also needs a *user* scope: a session cookie obtained via [`auth login`](auth-commands.md). The CLI is wallet-agnostic — pair it with `bb sign-arbitrary` for fully headless Cosmos signing, or paste in a signature from any external wallet.

## Agent Workflow: Query, Review, Transact

### Step 1: Discover available commands

```bash
# Get full command tree as JSON (for LLM tool discovery)
bb --help-json

# List all API route groups
bb api --help

# List routes in a specific group
bb api tokens --help
```

### Step 2: Query data

```bash
# Get a collection
bb api tokens get-collection 1

# Check a balance
bb api tokens get-balance --body '{"collectionId":"1","address":"bb1..."}'

# Look up an account
bb api accounts get-account --body '{"address":"bb1..."}'

# Search
bb api misc search --body '{"query":"my-token"}'
```

### Step 3: Review and audit

```bash
# Review a transaction before broadcasting
bb check tx.json

# Get human-readable explanation
bb explain tx.json

# Review a live collection
bb check 42 --testnet

# Interpret what a collection does
bb explain 42
```

### Step 4: Browse docs and skills

```bash
# Dump full docs for context
bb dev docs all

# Browse a specific topic
bb dev docs learn/approvals

# List available builder skills
bb dev skills

# Get instructions for building a specific token type
bb dev skills smart-token
```

### Step 5: Sign and broadcast (with chain binary)

```bash
# Create a key for the agent
bb keys add agent-wallet

# Sign and broadcast a transaction
bb tx tokenization create-collection ./collection.json \
  --from agent-wallet --chain-id bitbadges-1 \
  --node https://lcd.bitbadges.io:443 \
  --gas auto --gas-adjustment 1.5 --fees 10000ubadge

# Or broadcast via the API
bb api tx broadcast-tx --body @signed-tx.json
```

### Step 6: Authenticate for `Full Access` API routes

Most read-only API routes work with just the API key, but anything that mutates an account or publishes signed data needs a session cookie. The CLI ships a wallet-agnostic three-step flow that pairs cleanly with the chain binary's offline signer:

```bash
# 1. Fetch a challenge (saves the nonce cookie locally for step 3)
MSG=$(bb auth challenge --address $(bb keys show agent-wallet -a) | jq -r .data.message)

# 2. Sign offline with the chain binary
SIG_JSON=$(bb sign-arbitrary agent-wallet "$MSG")

# 3. Post the signature — stores the session under ~/.bitbadges/auth.json
bb auth login \
  --address    "$(echo "$SIG_JSON" | jq -r .address)" \
  --signature  "$(echo "$SIG_JSON" | jq -r .signature)" \
  --public-key "$(echo "$SIG_JSON" | jq -r .pubKey)" \
  --message    "$MSG"

# 4. Make Full Access requests by adding --with-session
bb api accounts get-account --body '{"address":"bb1..."}' --with-session
```

Multi-account, multi-network. Full reference: [Auth Commands](auth-commands.md).

## Combining with BitBadges Builder Tools

For AI assistants that speak MCP (Claude, Cursor, etc.), the [BitBadges Builder Tools](../ai-agents/builder-tools.md) provide 50+ higher-level tools for building token collections. The CLI and builder tools are complementary:

| Use Case | Tool |
|----------|------|
| Query data, browse docs, review transactions | **CLI** (`bb`) |
| Build token collections with guided workflows | **BitBadges Builder Tools** |
| Sign and broadcast transactions | **Chain binary** (`bb` or `bitbadgeschaind`) or **SDK Signing Client** |
| Key management | **Chain binary** (`bb keys ...`) |

## Example: Fully Automated Agent Script

```bash
#!/bin/bash
# Agent that checks a balance and conditionally transfers tokens

COLLECTION_ID=1
ADDRESS="bb1..."
THRESHOLD=10

# Query current balance
BALANCE=$(bb api tokens get-balance \
  --body "{\"collectionId\":\"$COLLECTION_ID\",\"address\":\"$ADDRESS\"}" \
  --condensed)

# Parse and decide — bb api emits the envelope; the body lives at .data
AMOUNT=$(echo "$BALANCE" | jq -r '.data.balances[0].amount // "0"')

if [ "$AMOUNT" -lt "$THRESHOLD" ]; then
  echo "Balance $AMOUNT below threshold $THRESHOLD, minting..."

  # Review the transaction first
  bb check ./mint-tx.json --testnet

  # If review passes, broadcast
  bb tx tokenization transfer-tokens ./mint-tx.json \
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

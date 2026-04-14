# Builder Tools Reference

The [BitBadges Builder](https://github.com/bitbadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/builder) provides 50+ tools for AI assistants to build, audit, and validate BitBadges transactions. It works with Claude Desktop, Claude Code, Cursor, and any MCP-compatible client.

> For terminal-based workflows without an MCP client, use the [BitBadges CLI](../cli/) — it provides 104+ API routes, review/audit tools, and built-in docs from the command line. See [CLI for AI Agents](../cli/for-ai-agents.md).

## Installation

```bash
# Install globally
npm install -g bitbadgesjs-sdk

# Or run directly (no install)
npx -p bitbadgesjs-sdk bitbadges-builder
```

### Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "bitbadges-builder": {
      "command": "npx",
      "args": ["-y", "-p", "bitbadgesjs-sdk", "bitbadges-builder"],
      "env": {
        "BITBADGES_API_KEY": "your-api-key",
        "BITBADGES_MNEMONIC": "your mnemonic phrase"
      }
    }
  }
}
```

### Claude Code

```bash
claude mcp add bitbadges-builder -- npx -y -p bitbadgesjs-sdk bitbadges-builder
```

### Cursor

Add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "bitbadges-builder": {
      "command": "npx",
      "args": ["-y", "-p", "bitbadgesjs-sdk", "bitbadges-builder"],
      "env": {
        "BITBADGES_API_KEY": "your-api-key"
      }
    }
  }
}
```

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `BITBADGES_API_KEY` | For queries/broadcast | Your BitBadges API key ([get one here](https://bitbadges.io/developer)) |
| `BITBADGES_MNEMONIC` | For signing | Mnemonic for server-side signing (testnet recommended) |
| `BITBADGES_PRIVATE_KEY` | For signing | Alternative: hex private key |

## Tools by Category

### Session-Based Per-Field Builders (Recommended)

The recommended way to build collections. Each tool sets one field on a session-scoped transaction, allowing incremental construction with full control. All tools can be called in parallel within the same round.

| Tool | Description |
|------|-------------|
| `set_standards` | Set the collection's protocol standards (e.g., "Subscription", "Smart Token") |
| `set_valid_token_ids` | Set which token IDs exist in the collection |
| `set_default_balances` | Set default balance configuration and user-level auto-approve flags |
| `set_permissions` | Set collection permissions (use presets like "locked-approvals" or custom) |
| `set_invariants` | Set collection-level invariants (e.g., noCustomOwnershipTimes, maxSupplyPerId) |
| `set_manager` | Set the collection manager address |
| `set_collection_metadata` | Set collection-level metadata (name, description, image) |
| `set_token_metadata` | Set per-token metadata URIs |
| `set_custom_data` | Set custom data on the collection |
| `add_approval` | Add a collection approval with approvalCriteria |
| `remove_approval` | Remove a collection approval by approvalId |
| `set_approval_metadata` | Set metadata for a specific approval |
| `add_alias_path` | Add an alias path (for ICS20-backed smart tokens) |
| `remove_alias_path` | Remove an alias path |
| `add_cosmos_wrapper_path` | Add a Cosmos wrapper path |
| `remove_cosmos_wrapper_path` | Remove a Cosmos wrapper path |
| `set_is_archived` | Set whether the collection is archived |
| `set_mint_escrow_coins` | Fund the mint escrow address at creation (for quest rewards, payment overrides) |
| `add_transfer` | Append a MsgTransferTokens to the transaction (for auto-minting at creation) |
| `remove_transfer` | Remove a transfer message from the transaction |
| `get_transaction` | Return the current session transaction as JSON |

### Helper Builders

Standalone tools for building specific non-collection resources.

| Tool | Description |
|------|-------------|
| `build_claim` | Build claim JSON for the API (code-gated, password-gated, whitelist-gated, open) |
| `build_address_list` | Build an on-chain address list collection |
| `build_transfer` | Build a MsgTransferTokens by auto-querying the collection and constructing the correct transaction |
| `build_dynamic_store` | Build transaction JSON for dynamic store operations (create, update, delete, set values) |

> **Note:** For building collections, use the session-based per-field tools above. They provide full control, support parallel calls, and are the recommended approach for all collection types.

### Audit & Analysis

| Tool | Description |
|------|-------------|
| `review_collection` | Unified review: audit, standards compliance, and UX findings in one call with audience filtering |
| `explain_collection` | Human-readable collection report with Q&A mode (user/developer/auditor audiences) |
| `analyze_collection` | Structured analysis of transferability, approvals, permissions, and how to obtain/transfer tokens |

### Simulation & Validation

| Tool | Description |
|------|-------------|
| `simulate_transaction` | Dry-run a transaction without broadcasting. Returns `parsedEvents` (coin, badge, and IBC transfers) and `netChanges` (per-address balance diffs) |
| `validate_transaction` | Validate a transaction is well-formed before broadcasting |

### Queries

| Tool | Description |
|------|-------------|
| `query_collection` | Fetch collection details from the BitBadges API |
| `query_balance` | Check token balance for an address |
| `query_dynamic_store` | Query on-chain dynamic store data (get store details, check address values, list values) |
| `verify_ownership` | Verify if an address meets ownership requirements |
| `search` | Search collections, accounts, and tokens |
| `search_plugins` | Search for off-chain claim plugins or fetch specific plugins by ID |
| `lookup_token_info` | Look up token metadata and info |

### Component Generators

Helper tools that generate specific pieces of a collection (approvals, permissions, addresses) without managing session state.

| Tool | Description |
|------|-------------|
| `generate_approval` | Generate approval structures by pattern (public-mint, manager-mint, smart-token-backing, subscription, etc.) |
| `generate_permissions` | Generate permission presets (fully-immutable, manager-controlled, locked-approvals) |
| `generate_backing_address` | Compute the deterministic IBC backing address for a given denom |
| `generate_alias_path` | Generate alias path config for token display on DEX |
| `generate_wrapper_address` | Compute the deterministic wrapper address for a custom denom |
| `generate_unique_id` | Generate collision-free IDs for approvals (prefix + random hash) |

### Utilities

| Tool | Description |
|------|-------------|
| `validate_address` | Validate a BitBadges address |
| `convert_address` | Convert between address formats (ETH, Cosmos, etc.) |
| `get_current_timestamp` | Get the current timestamp (for time-based configs) |
| `diagnose_error` | Diagnose BitBadges transaction errors and get suggested fixes |
| `search_knowledge_base` | Search across all BitBadges knowledge — docs, learnings, recipes, error patterns, critical rules |

### Instructions & Docs

| Tool | Description |
|------|-------------|
| `get_skill_instructions` | Get detailed build instructions for specific skills (smart-token, fungible-token, subscription, etc.) |
| `fetch_docs` | Fetch documentation pages |

## Workflows

### Session-Based Build (Recommended)

The per-field tools build up a collection incrementally in a session. This is the recommended approach — it gives full control and supports parallel tool calls.

```
set_standards + set_valid_token_ids + set_invariants + add_approval + set_permissions + set_default_balances + set_collection_metadata + set_token_metadata
  → (optional) add_transfer (for auto-mint at creation)
  → validate_transaction + audit_collection + simulate_transaction (in parallel)
  → fix errors with remove_approval + re-add (max 3 attempts)
  → get_transaction (final JSON)
```

**Step-by-step:**

1. **Build** — Call per-field tools in parallel to define the collection: standards, token IDs, invariants, approvals, permissions, metadata, balances.
2. **Auto-Mint** (optional) — If the user wants tokens minted to specific addresses at creation, call `add_transfer` to append a `MsgTransferTokens` alongside the collection creation.
3. **Verify** — Call `validate_transaction`, `audit_collection`, and `simulate_transaction` in parallel. Fix any errors with targeted `remove_approval` + re-add.
4. **Export** — Call `get_transaction` to get the final transaction JSON for signing.

### Query & Verification (No Signing)

```
query_collection → verify_ownership → (take action based on result)
```

> **Note:** The builder does not handle signing or broadcasting. Transaction signing must be done externally using the BitBadges SDK, a wallet, or the BitBadges frontend.

## Auto-Mint at Creation

The `add_transfer` tool lets you mint tokens to specific addresses at the same time as collection creation. This produces a transaction with two messages: `MsgUniversalUpdateCollection` + `MsgTransferTokens`.

**When to use:** "Mint 100 tokens to myself", "distribute tokens to team members", "auto-mint at creation".

**How it works:**
1. Build the collection normally with a mint approval (e.g., `add_approval` with `fromListId: "Mint"`, `initiatedByListId: <creator address>`)
2. Call `add_transfer` with the recipient addresses, balances, and `prioritizedApprovals` referencing the mint approval
3. Verify and export as normal — the transaction will contain both messages

Maximum 4 transfer messages per transaction.

## Skills (Guided Workflows)

The builder includes skill instructions accessible via `get_skill_instructions`. These guide AI agents through building specific collection types.

For full instructions and examples for each skill, see the **[Builder Skills reference](../../x-tokenization/examples/skills/README.md)**.

Common features like ownership requirements, codes, and passwords are built into the approval system directly and can be composed with any skill.

## Related Tools

| Tool | Install | Description |
|------|---------|-------------|
| **BitBadges SDK** | `npm i bitbadgesjs-sdk` | TypeScript SDK for direct blockchain interaction |
| **GitBook MCP** | [docs.bitbadges.io/~gitbook/mcp](https://docs.bitbadges.io/~gitbook/mcp) | Search BitBadges documentation |
| **BitBadges API** | [api.bitbadges.io](https://api.bitbadges.io) | REST API for queries and data |

## Builder Resources

The builder also exposes embedded documentation as resources:

| Resource URI | Name | Description |
|-------------|------|-------------|
| `bitbadges://tokens/registry` | Token Registry | IBC denoms, symbols, decimals, and pre-generated backing addresses |
| `bitbadges://rules/critical` | Critical Rules | Critical rules that must be followed when building transactions |
| `bitbadges://skills/all` | Skill Instructions | Instructions for all builder skills |
| `bitbadges://docs/concepts` | Core Concepts | Core BitBadges concepts: transferability, approvals, permissions, balances, and address lists |
| `bitbadges://docs/examples` | Full Examples | Complete transaction JSON examples for NFT collections, fungible tokens, and Smart Tokens |
| `bitbadges://recipes/all` | Code Recipes & Decision Matrices | Code snippets and decision matrices for common operations |
| `bitbadges://learnings/all` | Learnings & Gotchas | Known gotchas, tips, and discoveries from building with BitBadges |
| `bitbadges://errors/patterns` | Error Patterns | Common error messages mapped to diagnoses and fixes |
| `bitbadges://docs/frontend` | Reference Frontend Patterns | Patterns from the BitBadges reference frontend (Next.js + Ant Design) |
| `bitbadges://workflows/all` | Workflow Chains | Step-by-step tool chains for common multi-step operations |
| `bitbadges://schema/token-builder` | Token Builder Schema | Annotated schema for per-field session builders: design axes, field reference, approval patterns, and validation checklist |

Access these via your MCP client's resource reading capabilities.

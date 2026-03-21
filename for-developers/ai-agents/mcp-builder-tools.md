# MCP Builder Tools Reference

The [BitBadges Builder MCP server](https://github.com/BitBadges/bitbadges-builder-mcp) provides 30 tools for AI assistants to build, audit, and validate BitBadges transactions. It works with Claude Desktop, Claude Code, Cursor, and any MCP-compatible client.

## Installation

```bash
# Install globally
npm install -g bitbadges-builder-mcp

# Or run directly (no install)
npx bitbadges-builder-mcp
```

### Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "bitbadges-builder": {
      "command": "npx",
      "args": ["-y", "bitbadges-builder-mcp"],
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
claude mcp add bitbadges-builder -- npx -y bitbadges-builder-mcp
```

### Cursor

Add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "bitbadges-builder": {
      "command": "npx",
      "args": ["-y", "bitbadges-builder-mcp"],
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

### High-Level Builders

| Tool | Description |
|------|-------------|
| `build_smart_token` | Build a complete smart token (badge) collection transaction |
| `build_fungible_token` | Build a fungible token collection transaction |
| `build_nft_collection` | Build an NFT collection transaction |
| `build_token` | Universal token builder -- single entry point for all collection types. Set tokenType for simplified inputs, or use the full design axes directly |
| `build_claim` | Build claim JSON for the API (code-gated, password-gated, whitelist-gated, open) |
| `build_address_list` | Build an on-chain address list collection. List membership = owning x1 of token ID 1 |
| `build_transfer` | Build a MsgTransferTokens by auto-querying the collection, analyzing its approvals, and constructing the correct transaction |
| `build_dynamic_store` | Build transaction JSON for dynamic store operations: create, update, delete, or set values for on-chain allowlists/blocklists |

### Audit & Analysis

| Tool | Description |
|------|-------------|
| `audit_collection` | Security audit: centralization, supply inflation, approval flaws, common bugs (9 categories) |
| `explain_collection` | Human-readable collection report with Q&A mode (user/developer/auditor audiences) |
| `analyze_collection` | Query a collection and produce a structured analysis of its transferability, approvals, permissions, and how to obtain/transfer tokens |

### Component Builders

| Tool | Description |
|------|-------------|
| `generate_backing_address` | Generate a backing address for IBC-backed smart tokens |
| `generate_approval` | Generate an approval configuration |
| `generate_permissions` | Generate permission configurations |
| `generate_alias_path` | Generate alias path for address lookups |

### Simulation & Validation

| Tool | Description |
|------|-------------|
| `simulate_transaction` | Dry-run a transaction without broadcasting |
| `validate_transaction` | Validate a transaction before broadcasting |

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

### Utilities

| Tool | Description |
|------|-------------|
| `validate_address` | Validate a BitBadges address |
| `convert_address` | Convert between address formats (ETH, Cosmos, etc.) |
| `get_current_timestamp` | Get the current timestamp (for time-based configs) |
| `diagnose_error` | Diagnose BitBadges transaction errors and get suggested fixes |
| `search_knowledge_base` | Search across all BitBadges knowledge -- embedded docs, learnings, recipes, error patterns, and critical rules |

### Instructions & Docs

| Tool | Description |
|------|-------------|
| `get_skill_instructions` | Get detailed instructions for specific skills (smart-token, fungible-token, nft-collection, subscription) |
| `fetch_docs` | Fetch documentation pages |

## Typical Workflow

The recommended Build → Audit → Validate → Simulate pipeline:

1. **Build** - Use a builder tool (`build_fungible_token`, `build_nft_collection`, `build_smart_token`, or `build_token`) to generate transaction JSON
2. **Audit** - Use `audit_collection` to check for security risks and common bugs
3. **Fix** - Address any critical/warning findings, re-audit if needed
4. **Validate** - Use `validate_transaction` to check the transaction is well-formed
5. **Simulate** - Use `simulate_transaction` to dry-run and estimate gas
6. **Broadcast** - The user signs and broadcasts manually via the BitBadges SDK or frontend

```
build_token → audit_collection → validate_transaction → simulate_transaction → (user broadcasts via SDK/frontend)
```

For queries and verification (no signing needed):

```
query_collection → verify_ownership → (take action based on result)
```

> **Note:** The MCP server does not handle signing or broadcasting. Transaction signing must be done externally using the BitBadges SDK, a wallet, or the BitBadges frontend.

## Skills (Guided Workflows)

The MCP server includes 17 skill instructions accessible via `get_skill_instructions`. These guide AI agents through complex tasks:

| Skill ID | Name | Description |
|----------|------|-------------|
| `smart-token` | Smart Token | IBC-backed smart token with 1:1 backing and two required approvals (backing + unbacking) |
| `minting` | Minting | Mint approval patterns including public mint, whitelist mint, creator-only mint, payment-gated mint, and escrow payouts |
| `liquidity-pools` | Liquidity Pools | Liquidity pool standard with the "Liquidity Pools" protocol standard tag |
| `fungible-token` | Fungible Token | Simple fungible token with fixed or unlimited supply and configurable mint/transfer approvals |
| `nft-collection` | NFT Collection | Non-fungible token collection with unique token IDs, metadata URIs, and badge-based ownership |
| `subscription` | Subscription | Time-based subscription token with recurring payment approvals and auto-deletion on expiry |
| `immutability` | Transferability & Update Rules | Lock collection permissions to make properties permanently immutable or permanently permitted |
| `custom-2fa` | Custom 2FA | Two-factor authentication for transfers using a secondary approval address |
| `address-list` | Address List | On-chain managed address list where membership = owning x1 of token ID 1 |
| `bb-402` | BB-402 Token-Gated Access | Token-gated access protocol where ownership of specific badges grants API/resource access |
| `burnable` | Burnable | Allow token holders to burn tokens by sending them to the burn address |
| `multi-sig-voting` | Multi-Sig / Voting | Require weighted quorum voting from multiple parties before transfers can proceed |
| `ai-criteria-gate` | AI Criteria Gate | AI-evaluated criteria gate using attestation NFTs and dynamic store for automated access decisions |
| `verified` | Verified Gate | Gate access based on BitBadges verified credential badges (collection ID 1) |
| `payment-protocol` | Payment Protocol | Invoices, escrows, bounties, and payment receipts using ListView standard and coinTransfer-based approvals |
| `tradable` | Tradable NFTs | Tradable token standard enabling peer-to-peer transfers with the "Tradable" protocol standard tag |
| `credit-token` | Credit Token | Increment-only, non-transferable credit token purchased with any ICS20 denom |

## Related Tools

| Tool | Install | Description |
|------|---------|-------------|
| **BitBadges SDK** | `npm i bitbadgesjs-sdk` | TypeScript SDK for direct blockchain interaction |
| **GitBook MCP** | [docs.bitbadges.io/~gitbook/mcp](https://docs.bitbadges.io/~gitbook/mcp) | Search BitBadges documentation |
| **BitBadges API** | [api.bitbadges.io](https://api.bitbadges.io) | REST API for queries and data |

## MCP Resources

The MCP server also exposes embedded documentation as resources:

| Resource URI | Name | Description |
|-------------|------|-------------|
| `bitbadges://tokens/registry` | Token Registry | IBC denoms, symbols, decimals, and pre-generated backing addresses |
| `bitbadges://rules/critical` | Critical Rules | Critical rules that must be followed when building transactions |
| `bitbadges://skills/all` | Skill Instructions | Instructions for all 17 builder skills |
| `bitbadges://docs/concepts` | Core Concepts | Core BitBadges concepts: transferability, approvals, permissions, balances, and address lists |
| `bitbadges://docs/examples` | Full Examples | Complete transaction JSON examples for NFT collections, fungible tokens, and Smart Tokens |
| `bitbadges://recipes/all` | Code Recipes & Decision Matrices | Code snippets and decision matrices for common operations |
| `bitbadges://learnings/all` | Learnings & Gotchas | Known gotchas, tips, and discoveries from building with BitBadges |
| `bitbadges://errors/patterns` | Error Patterns | Common error messages mapped to diagnoses and fixes |
| `bitbadges://docs/frontend` | Reference Frontend Patterns | Patterns from the BitBadges reference frontend (Next.js + Ant Design) |
| `bitbadges://workflows/all` | Workflow Chains | Step-by-step tool chains for common multi-step operations |
| `bitbadges://schema/token-builder` | Token Builder Schema | Annotated schema for build_token: design axes, field reference, approval patterns, and validation checklist |

Access these via your MCP client's resource reading capabilities.

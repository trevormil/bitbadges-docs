# MCP Builder Tools Reference

The [BitBadges Builder MCP server](https://github.com/BitBadges/bitbadges-builder-mcp) provides 30+ tools for AI assistants to build, audit, sign, and broadcast BitBadges transactions. It works with Claude Desktop, Claude Code, Cursor, and any MCP-compatible client.

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
| `build_claim` | Build claim JSON for the API (code-gated, password-gated, whitelist-gated, open) |

### Audit & Analysis

| Tool | Description |
|------|-------------|
| `audit_collection` | Security audit: centralization, supply inflation, approval flaws, common bugs (9 categories) |
| `explain_collection` | Human-readable collection report with Q&A mode (user/developer/auditor audiences) |

### Component Builders

| Tool | Description |
|------|-------------|
| `generate_backing_address` | Generate a backing address for off-chain balances |
| `generate_approval` | Generate an approval configuration |
| `generate_permissions` | Generate permission configurations |
| `generate_alias_path` | Generate alias path for address lookups |

### Signing & Broadcasting

| Tool | Description |
|------|-------------|
| `sign_and_broadcast` | Sign a transaction and broadcast it (recommended) |
| `broadcast` | Broadcast a pre-signed transaction |
| `simulate_transaction` | Dry-run a transaction without broadcasting |

### Queries

| Tool | Description |
|------|-------------|
| `query_collection` | Fetch collection details from the BitBadges API |
| `query_balance` | Check token balance for an address |
| `verify_ownership` | Verify if an address meets ownership requirements |
| `search` | Search collections, accounts, and tokens |
| `lookup_token_info` | Look up token metadata and info |

### Utilities

| Tool | Description |
|------|-------------|
| `validate_transaction` | Validate a transaction before broadcasting |
| `validate_address` | Validate a BitBadges address |
| `convert_address` | Convert between address formats (ETH, Cosmos, etc.) |
| `get_current_timestamp` | Get the current timestamp (for time-based configs) |
| `publish_to_bitbadges` | Publish metadata to BitBadges |

### Instructions & Docs

| Tool | Description |
|------|-------------|
| `get_skill_instructions` | Get detailed instructions for specific skills (smart-token, fungible-token, nft-collection, subscription) |
| `get_master_prompt` | Get the master system prompt for BitBadges building |
| `fetch_docs` | Fetch documentation pages |

## Key Tool Schemas

### `sign_and_broadcast`

The primary tool for autonomous transaction execution. Signs with a private key or mnemonic and broadcasts to the network.

**Input:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `transactionJson` | string | Yes | Full transaction JSON from builder tools (with messages array, optional memo and fee) |
| `signingMethod` | `'privateKey'` \| `'mnemonic'` | Yes | Signing method |
| `credential` | string | Yes | Private key (hex) or mnemonic phrase |
| `network` | `'mainnet'` \| `'testnet'` \| `'local'` | No | Default: `'testnet'` (safe default for agents) |
| `apiKey` | string | No | Falls back to `BITBADGES_API_KEY` env var |

**Output:**

```json
{
  "success": true,
  "txHash": "ABC123...",
  "signerAddress": "bb1...",
  "network": "testnet",
  "gasUsed": "150000",
  "fee": { "amount": "5000", "denom": "ubadge", "gas": "500000" },
  "explorerUrl": "https://explorer.bitbadges.io/BitBadges%20Testnet/tx/ABC123...",
  "signingDetails": {
    "chainType": "cosmos",
    "cosmosChainId": "bitbadges-2",
    "accountNumber": 42,
    "sequence": 7,
    "messagesCount": 1,
    "messageTypes": ["MsgCreateCollection"]
  }
}
```

### `broadcast`

For pre-signed transactions (e.g., signed externally via SDK, hardware wallet, or custodial service).

**Input:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `signedTxBody` | string | Yes | Signed transaction body as JSON (standard Cosmos `BroadcastTxRequest` format with `tx_bytes` and `mode`) |
| `network` | `'mainnet'` \| `'testnet'` \| `'local'` | No | Default: `'testnet'` |
| `apiKey` | string | No | Falls back to `BITBADGES_API_KEY` env var |

**Output:**

```json
{
  "success": true,
  "txHash": "ABC123...",
  "code": 0,
  "explorerUrl": "https://explorer.bitbadges.io/BitBadges%20Testnet/tx/ABC123..."
}
```

## Typical Workflow

The recommended Build → Audit → Deploy pipeline:

1. **Build** - Use a builder tool (`build_fungible_token`, `build_nft_collection`, `build_smart_token`) to generate transaction JSON
2. **Audit** - Use `audit_collection` to check for security risks and common bugs
3. **Fix** - Address any critical/warning findings, re-audit if needed
4. **Validate** - Use `validate_transaction` to check the transaction is well-formed
5. **Simulate** - Use `simulate_transaction` to dry-run and estimate gas
6. **Sign & Broadcast** - Use `sign_and_broadcast` to execute the transaction

```
build_fungible_token → audit_collection → validate_transaction → simulate_transaction → sign_and_broadcast
```

For queries and verification (no signing needed):

```
query_collection → verify_ownership → (take action based on result)
```

## Skills (Guided Workflows)

The MCP server includes 30+ skill instructions accessible via `get_skill_instructions`. These guide AI agents through complex tasks:

| Skill | Description |
|-------|-------------|
| `smart-token` | Build IBC-backed smart tokens (stablecoins, wrapped assets) |
| `fungible-token` | Build ERC-20 style fungible tokens |
| `nft-collection` | Build NFT collections with minting config |
| `collection-audit` | Audit collections for security risks |
| `explain-collection` | Generate human-readable collection reports |
| `update-collection` | Update existing collections (permissions, metadata, approvals) |
| `collection-recipes` | Decision matrix: which builder tool for which use case |
| `ibc-and-hooks` | IBC integration, custom hooks, ICQ queries |
| `permissions` | Permission system deep-dive |
| `approval` | Approval configuration patterns |
| `subscription` | Subscription/recurring token patterns |

## Related Tools

| Tool | Install | Description |
|------|---------|-------------|
| **BitBadges SDK** | `npm i bitbadgesjs-sdk` | TypeScript SDK for direct blockchain interaction |
| **GitBook MCP** | [docs.bitbadges.io/~gitbook/mcp](https://docs.bitbadges.io/~gitbook/mcp) | Search BitBadges documentation |
| **BitBadges API** | [api.bitbadges.io](https://api.bitbadges.io) | REST API for queries and data |

## MCP Resources

The MCP server also exposes embedded documentation as resources:

| Resource URI | Description |
|-------------|-------------|
| `bitbadges://rules/critical` | Critical rules for transaction building |
| `bitbadges://docs/concepts` | Core concepts (approvals, permissions, balances) |
| `bitbadges://docs/sdk` | SDK reference |
| `bitbadges://docs/api` | API reference |
| `bitbadges://docs/messages` | Message type reference |
| `bitbadges://docs/examples` | Example transactions |

Access these via your MCP client's resource reading capabilities.

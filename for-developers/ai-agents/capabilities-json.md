# Machine Discovery

How can AI agents discover what BitBadges can do and how to interact with it? This page describes the current mechanisms and a proposed convention for structured capability discovery.

## Current Mechanisms

BitBadges already provides several machine-readable entry points:

| Mechanism | URL / Location | What It Provides |
|-----------|---------------|-----------------|
| **for-llms.txt** | [docs.bitbadges.io/for-llms.txt](https://docs.bitbadges.io/for-llms.txt) | Full documentation as plain text, optimized for LLM context windows |
| **OpenAPI Spec** | [GitHub](https://raw.githubusercontent.com/bitbadges/bitbadgesjs/main/packages/bitbadgesjs-sdk/openapi-hosted/openapi.json) | Complete API schema with all endpoints, request/response types |
| **MCP Server** | `npx bitbadges-builder-mcp` | 23+ tools discoverable via MCP protocol's `tools/list` |
| **MCP Resources** | `bitbadges://docs/*` | Documentation pages as MCP resources |
| **GitBook MCP** | `docs.bitbadges.io/~gitbook/mcp` | GitBook-provided `searchDocumentation` tool |

These are sufficient for most AI agents today. An LLM with access to the MCP server can discover all available tools, and the OpenAPI spec provides complete API coverage.

## Proposed: capabilities.json

For more structured, automated discovery, we propose a `capabilities.json` manifest that agents could fetch to understand the full integration surface at a glance.

### Proposed Schema

```json
{
  "name": "BitBadges",
  "version": "1.0.0",
  "description": "Digital token infrastructure for badges, credentials, and access control",
  "capabilities": {
    "tokens": {
      "create": true,
      "transfer": true,
      "burn": true,
      "query": true,
      "types": ["fungible", "non-fungible", "semi-fungible"]
    },
    "claims": {
      "create": true,
      "verify": true,
      "gate": true
    },
    "identity": {
      "addressFormats": ["cosmos", "ethereum", "solana", "bitcoin"],
      "verification": true,
      "addressLists": true
    }
  },
  "integrationPoints": {
    "api": {
      "baseUrl": "https://api.bitbadges.io",
      "specUrl": "https://raw.githubusercontent.com/bitbadges/bitbadgesjs/main/packages/bitbadgesjs-sdk/openapi-hosted/openapi.json",
      "authentication": "apiKey"
    },
    "mcp": {
      "package": "bitbadges-builder-mcp",
      "toolCount": 23,
      "signingCapable": true
    },
    "sdk": {
      "package": "bitbadgesjs-sdk",
      "language": "typescript",
      "signingCapable": true
    },
    "websocket": {
      "mainnet": "wss://rpc.bitbadges.io/websocket",
      "testnet": "wss://rpc-testnet.bitbadges.io/websocket"
    }
  },
  "networks": {
    "mainnet": {
      "apiUrl": "https://api.bitbadges.io",
      "chainId": "bitbadges-1",
      "evmChainId": 50024
    },
    "testnet": {
      "apiUrl": "https://api.bitbadges.io/testnet",
      "chainId": "bitbadges-2",
      "evmChainId": 50025,
      "faucet": "https://api.bitbadges.io/testnet/api/v0/faucet"
    }
  }
}
```

### How Agents Would Use It

1. **Discovery:** Agent fetches `https://bitbadges.io/capabilities.json` (or a well-known URL)
2. **Capability Check:** Agent inspects `capabilities` to determine if BitBadges supports the needed operation
3. **Integration Selection:** Agent chooses the best integration point (API, MCP, SDK) based on its runtime
4. **Configuration:** Agent uses `networks` to connect to the correct environment

### Status

This is a **proposed convention**, not yet deployed. The current mechanisms (`for-llms.txt`, MCP `tools/list`, OpenAPI spec) serve the same purpose and are available today.

If you'd like to see this convention adopted, or have feedback on the schema, reach out on [Discord](https://discord.gg/bitbadges) or open an issue on [GitHub](https://github.com/BitBadges).

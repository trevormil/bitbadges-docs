# AI Agents & Bots

This section is for developers building AI agents, bots, and automated systems that interact with the BitBadges blockchain. Whether you're building an autonomous minting agent, a gating bot, or an AI-powered claim system, you'll find everything you need here.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/install.sh | sh
```

This installs the chain binary and SDK CLI. For CLI-based agent workflows (query, review, transact — no TypeScript needed), see [CLI for AI Agents](../cli/for-ai-agents.md).

## 5-Minute Quickstart (TypeScript)

```bash
npm install bitbadgesjs-sdk
```

```typescript
import { BitBadgesSigningClient, GenericEvmAdapter, MsgTransferTokens, NETWORK_CONFIGS } from 'bitbadgesjs-sdk';

// 1. Create adapter from mnemonic (server-side)
const adapter = await GenericEvmAdapter.fromMnemonic(
  'your twelve word mnemonic phrase here ...',
  NETWORK_CONFIGS['testnet'].evmRpcUrl
);

// 2. Create signing client (testnet)
const client = new BitBadgesSigningClient({
  adapter,
  network: 'testnet'
});

// 3. Get testnet tokens
await fetch('https://api.bitbadges.io/testnet/api/v0/faucet', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ address: client.address })
});

// 4. Broadcast your first transaction
const result = await client.signAndBroadcast([
  MsgTransferTokens.create({
    creator: client.address,
    collectionId: '1',
    transfers: [/* ... */]
  })
]);

console.log('TX Hash:', result.txHash);
```

## Integration Paths

| Path | Best For | Install |
|------|----------|---------|
| **CLI & Chain Binary** | Terminal agents, shell scripts, any language | `curl -fsSL .../install.sh \| sh` — [guide](../cli/for-ai-agents.md) |
| **BitBadges Builder Tools** | AI assistants (Claude, Cursor, etc.) | `npm i -g bitbadgesjs-sdk` |
| **SDK Signing Client** | Full-featured TypeScript bots | `npm i bitbadgesjs-sdk` |
| **Direct HTTP** | Lightweight scripts, any language | REST calls to `api.bitbadges.io` |
| **Agent Spending Authorization** | Set daily caps, time windows, and revocation | [guide](agent-spending-authorization.md) |

### Quick Setup

```bash
# Claude Code (one command)
claude mcp add bitbadges-builder -- npx -y -p bitbadgesjs-sdk bitbadges-builder

# Or install globally
npm install -g bitbadgesjs-sdk
```

See the full [Builder Tools Reference](builder-tools.md) for all 50+ tools (including session-based per-field builders), configuration for Claude Desktop / Cursor, and workflow guides.

## Network Configuration

| Network | API URL | Node LCD | Cosmos Chain ID | EVM Chain ID | EVM RPC |
|---------|---------|----------|-----------------|--------------|---------|
| **Mainnet** | `https://api.bitbadges.io` | `https://lcd.bitbadges.io` | `bitbadges-1` | 50024 | `https://evm-rpc.bitbadges.io` |
| **Testnet** | `https://api.bitbadges.io/testnet` | `https://lcd-testnet.bitbadges.io` | `bitbadges-2` | 50025 | `https://evm-rpc-testnet.bitbadges.io` |
| **Local** | `http://localhost:3001` | `http://localhost:1317` | `bitbadges-1` | 90123 | `http://localhost:8545` |

Additional endpoints (testnet):
- **RPC:** `https://rpc-testnet.bitbadges.io`
- **EVM RPC:** `https://evm-rpc-testnet.bitbadges.io`
- **WebSocket:** `wss://rpc-testnet.bitbadges.io/websocket`

## Section Contents

| Page | Description |
|------|-------------|
| [Testnet Faucet API](testnet-faucet.md) | Get free testnet BADGE tokens for your bot |
| [Builder Tools Reference](builder-tools.md) | All 50+ builder tools for AI assistants |
| [WebSocket Events](websocket-events.md) | Subscribe to real-time blockchain events |
| [Bot Examples](bot-examples.md) | Copy-paste examples for common bot patterns |
| [E2E: AI Agent with USDC Vault](openclaw-vault-tutorial.md) | Full tutorial: wallet setup, vault rules, withdraw/deposit |

## BB-402: Token-Gated API Access

BB-402 lets any server gate API access behind on-chain token ownership using the standard HTTP 402 status code. Unlike x402 (Coinbase) which only supports per-request USDC payments, BB-402 uses token ownership as a universal primitive -- a soulbound token costing X USDC is a verifiable on-chain receipt (equivalent to x402), but the same protocol also handles subscriptions, tiered access, reputation, blocklists, and compound conditions with `$and`/`$or` logic.

```
Agent --> Server:  GET /api/data
Server --> Agent:  402 { ownershipRequirements, message }
Agent --> Server:  GET /api/data + X-BB-Proof: { address, chain, message, signature }
Server --> Agent:  200 OK (or 403)
```

See the full [BB-402 guide and quickstart](../../token-standard/bb-402/overview.md) in the Token Standard section, or the [complete specification](../../token-standard/bb-402/spec.md).

## Further Reading

- [Signing Client Reference](../bitbadges-blockchain/create-and-broadcast-txs/signing-client.md) - Full signing client documentation
- [API Getting Started](../bitbadges-api/) - REST API reference
- [Testnet Mode](../bitbadges-blockchain/testnet-mode.md) - Testnet environment details
- [BitBadges AI Quickstarter](https://github.com/BitBadges/bitbadges-quickstarter-ai) - GitHub template repo
- [SDK AI Agent Guide](https://github.com/BitBadges/bitbadgesjs/blob/main/packages/bitbadgesjs-sdk/AI_AGENT_GUIDE.md) - SDK-specific AI guide

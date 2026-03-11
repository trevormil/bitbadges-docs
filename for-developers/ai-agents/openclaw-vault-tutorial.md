# E2E Tutorial: AI Agent with a USDC Vault

This tutorial walks through setting up an AI agent (e.g., OpenClaw or any framework) with its own USDC-backed smart token vault on BitBadges. The agent gets a wallet, a vault with configurable rules, and the ability to withdraw funds programmatically.

## Architecture Overview

```
Your AI Agent (OpenClaw, etc.)
    │
    ├── Wallet (Cosmos or EVM key pair)
    │
    └── BitBadges Smart Token Vault
         ├── 1:1 USDC backing
         ├── Spending rules (limits, whitelists, etc.)
         └── Withdraw via MsgTransferTokens
```

The agent holds vault tokens. To spend, it withdraws (unbacking) tokens back to USDC by sending a `MsgTransferTokens` to the vault's backing address. The vault rules (daily limits, recipient whitelists, 2FA thresholds, etc.) are enforced at the protocol level.

## Step 1: Create the Vault (Website)

Go to the BitBadges website **Create** tab and select **Smart Token**.

1. Configure a **1:1 USDC-backed** smart token
2. Add your custom rules (spending limits, recipient whitelists, time gates, etc.)
3. Optionally check **AI Agent Vault** to enable the AI Prompt tab on the token view page
4. Submit the transaction to create the collection

After creation, note your **Collection ID** - you'll need it for the agent.

## Step 2: Set Up the Agent Wallet

Your AI agent needs a wallet to sign transactions. You can use either a Cosmos (mnemonic) or EVM (private key) approach.

### Option A: EVM Wallet (Recommended for Server-Side)

Generate a mnemonic and derive a BitBadges address:

```typescript
import { BitBadgesSigningClient, GenericEvmAdapter, NETWORK_CONFIGS } from 'bitbadgesjs-sdk';

// Store this mnemonic securely (env var, secret manager, etc.)
const adapter = await GenericEvmAdapter.fromMnemonic(
  process.env.AGENT_MNEMONIC!, // 12 or 24 word mnemonic
  NETWORK_CONFIGS['mainnet'].evmRpcUrl // 'https://evm-rpc.bitbadges.io'
);

const client = new BitBadgesSigningClient({
  adapter,
  network: 'mainnet' // or 'testnet'
});

console.log('Agent address:', client.address); // 0x...
```

### Option B: EVM Wallet from Private Key

```typescript
import { GenericEvmAdapter, NETWORK_CONFIGS } from 'bitbadgesjs-sdk';

const adapter = await GenericEvmAdapter.fromPrivateKey(
  process.env.AGENT_PRIVATE_KEY!, // hex private key (with or without 0x prefix)
  NETWORK_CONFIGS['mainnet'].evmRpcUrl
);

const client = new BitBadgesSigningClient({ adapter });
```

### Option C: Cosmos Wallet

```typescript
import { BitBadgesSigningClient, GenericCosmosAdapter } from 'bitbadgesjs-sdk';

const adapter = await GenericCosmosAdapter.fromMnemonic(
  process.env.AGENT_MNEMONIC!,
  'bitbadges-1' // mainnet (or 'bitbadges-2' for testnet)
);

const client = new BitBadgesSigningClient({
  adapter,
  network: 'mainnet'
});

console.log('Agent address:', client.address); // bb1...
```

> **Note:** The same mnemonic produces a different address with the Cosmos adapter vs EVM adapter. Make sure you fund the correct address for your chosen adapter.

### Fund the Wallet

The agent address needs a small amount of `$BADGE` for gas fees. Transfer some from your main wallet, or use the [testnet faucet](testnet-faucet.md) if testing:

```typescript
await fetch('https://api.bitbadges.io/testnet/api/v0/faucet', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ address: client.address })
});
```

### Deposit USDC into the Vault

Transfer USDC to the vault's backing address, then mint vault tokens to the agent's address. This is the deposit (backing) operation - see the AI Prompt tab on your token's page for the exact transaction format with your vault's specific approval IDs and versions.

## Step 3: Understand Vault Rules

Your vault's transferability rules determine what the agent can and can't do. These are enforced at the protocol level - the agent cannot bypass them regardless of what code it runs.

### Reading Rules Programmatically

```typescript
import { BitBadgesAPI } from 'bitbadgesjs-sdk';

const api = new BitBadgesAPI({ apiUrl: 'https://api.bitbadges.io' });

const collectionRes = await api.getCollection({ collectionId: 'YOUR_COLLECTION_ID' });
const collection = collectionRes.collection;

// The collectionApprovals array defines all transfer rules
const approvals = collection.collectionApprovals;

for (const approval of approvals) {
  console.log(`Approval: ${approval.approvalId}`);
  console.log(`  From: ${approval.fromListId}`);
  console.log(`  To: ${approval.toListId}`);
  console.log(`  Criteria:`, JSON.stringify(approval.approvalCriteria, null, 2));
}
```

### Common Rule Types

| Rule | What It Does | Protocol Enforcement |
|------|-------------|---------------------|
| Daily spending limit | Caps total amount per day | `approvalAmounts` with `resetTimeIntervals` |
| Recipient whitelist | Only approved addresses | `toListId` pointing to an address list |
| Transaction rate limit | Max N transfers per period | `maxNumTransfers` with `resetTimeIntervals` |
| 2FA for large amounts | Human approval above threshold | ETH signature challenges or external checks |
| Emergency freeze | Halt all activity | Locked `canUpdateCollectionApprovals` permission |

### Using the AI Prompt Tab

If you checked "AI Agent Vault" when creating your token, the token view page on BitBadges has an **AI Prompt** tab. This generates a ready-to-use prompt containing:

- Collection ID and token name
- Backing address
- Denomination (USDC)
- Exact approval IDs and versions for deposit/withdraw
- Step-by-step deposit and withdraw instructions

Copy this prompt and provide it to your AI agent as part of its system instructions or context.

## Step 4: Withdraw from the Vault (Spending)

Withdrawing converts vault tokens back to USDC. The agent sends its vault tokens **to** the backing address, and the protocol releases equivalent USDC to the agent's account.

### Building the Withdraw Transaction

```typescript
import { MsgTransferTokens } from 'bitbadgesjs-sdk';

const COLLECTION_ID = 'YOUR_COLLECTION_ID';
const BACKING_ADDRESS = 'bb1...'; // The vault's backing address (from AI Prompt tab)
const WITHDRAW_APPROVAL_ID = 'smart-account-unbacking'; // Check your vault's approval IDs
const WITHDRAW_VERSION = '0'; // Check your vault's approval versions

// Withdraw 10 USDC worth of vault tokens
// Note: amounts are in base units. For USDC (6 decimals), 10 USDC = 10000000 base units
const withdrawMsg = MsgTransferTokens.create({
  creator: client.address,
  collectionId: COLLECTION_ID,
  transfers: [{
    from: client.address,
    toAddresses: [BACKING_ADDRESS],
    balances: [{
      amount: 10000000n, // 10 USDC in base units
      tokenIds: [{ start: 1n, end: 1n }],
      ownershipTimes: [{ start: 1n, end: 18446744073709551615n }]
    }],
    prioritizedApprovals: [{
      approvalId: WITHDRAW_APPROVAL_ID,
      approvalLevel: 'collection',
      approverAddress: '',
      version: WITHDRAW_VERSION
    }],
    onlyCheckPrioritizedCollectionApprovals: true,
    merkleProofs: [],
    ethSignatureProofs: [],
    memo: ''
  }]
});
```

### Signing and Broadcasting

```typescript
const result = await client.signAndBroadcast([withdrawMsg]);

if (result.success) {
  console.log('Withdrawal successful! TX:', result.txHash);
  // USDC is now in the agent's account
} else {
  console.error('Withdrawal failed:', result.error);
  // Common failures:
  // - Exceeds daily spending limit
  // - Recipient not on whitelist
  // - Insufficient vault balance
  // - Rate limit exceeded
}
```

### Depositing Back (Backing)

To deposit USDC back into the vault, the direction is reversed - transfer **from** the backing address **to** your address:

```typescript
const DEPOSIT_APPROVAL_ID = 'smart-account-backing'; // Check your vault's approval IDs
const DEPOSIT_VERSION = '0';

const depositMsg = MsgTransferTokens.create({
  creator: client.address,
  collectionId: COLLECTION_ID,
  transfers: [{
    from: BACKING_ADDRESS,
    toAddresses: [client.address],
    balances: [{
      amount: 5000000n, // 5 USDC in base units
      tokenIds: [{ start: 1n, end: 1n }],
      ownershipTimes: [{ start: 1n, end: 18446744073709551615n }]
    }],
    prioritizedApprovals: [{
      approvalId: DEPOSIT_APPROVAL_ID,
      approvalLevel: 'collection',
      approverAddress: '',
      version: DEPOSIT_VERSION
    }],
    onlyCheckPrioritizedCollectionApprovals: true,
    merkleProofs: [],
    ethSignatureProofs: [],
    memo: ''
  }]
});

const result = await client.signAndBroadcast([depositMsg]);
```

## Step 5: Integrate with Your Agent Framework

Wire the wallet and vault operations into your AI agent's tool set. The specifics depend on your framework (OpenClaw, LangChain, custom, etc.).

### Example: Expose as Agent Tools

```typescript
// These are the tools your agent needs
const agentTools = {
  checkBalance: async () => {
    const api = new BitBadgesAPI({ apiUrl: 'https://api.bitbadges.io' });
    const res = await api.getBalance({
      collectionId: COLLECTION_ID,
      address: client.address
    });
    return res.balance;
  },

  withdraw: async (amountBaseUnits: bigint) => {
    const msg = MsgTransferTokens.create({
      creator: client.address,
      collectionId: COLLECTION_ID,
      transfers: [{
        from: client.address,
        toAddresses: [BACKING_ADDRESS],
        balances: [{
          amount: amountBaseUnits,
          tokenIds: [{ start: 1n, end: 1n }],
          ownershipTimes: [{ start: 1n, end: 18446744073709551615n }]
        }],
        prioritizedApprovals: [{
          approvalId: WITHDRAW_APPROVAL_ID,
          approvalLevel: 'collection',
          approverAddress: '',
          version: WITHDRAW_VERSION
        }],
        onlyCheckPrioritizedCollectionApprovals: true,
        merkleProofs: [],
        ethSignatureProofs: [],
        memo: ''
      }]
    });
    return client.signAndBroadcast([msg]);
  },

  getVaultRules: async () => {
    const api = new BitBadgesAPI({ apiUrl: 'https://api.bitbadges.io' });
    const res = await api.getCollection({ collectionId: COLLECTION_ID });
    return res.collection.collectionApprovals;
  }
};
```

### Providing Context to the Agent

Give your agent the prompt from the **AI Prompt** tab (Step 3) as part of its system context. This tells the agent what vault it has access to, what the rules are, and how to construct transactions.

## Key Concepts Summary

| Concept | Details |
|---------|---------|
| **Vault tokens** | Smart tokens backed 1:1 by USDC. Agent holds these. |
| **Backing address** | Protocol-controlled escrow. Holds the actual USDC. |
| **Withdraw (unback)** | Send vault tokens TO backing address -> receive USDC |
| **Deposit (back)** | Transfer FROM backing address to your address -> receive vault tokens |
| **Rules** | Encoded in `collectionApprovals`. Protocol-enforced, not bypassable. |
| **Gas** | Agent needs `$BADGE` for tx fees (separate from USDC vault balance) |

## Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| Insufficient balance | Not enough vault tokens | Check balance, deposit more USDC |
| No valid approval | Wrong approval ID/version | Check AI Prompt tab for correct values |
| Amount exceeds limit | Hit daily spending cap | Wait for reset period or reduce amount |
| Sequence mismatch | Nonce out of sync | Client retries automatically (up to 3x) |
| Insufficient gas | Not enough $BADGE | Fund the agent address with $BADGE |

## Further Reading

- [Signing Client Reference](../bitbadges-blockchain/create-and-broadcast-txs/signing-client.md) - Full signing client docs
- [MsgTransferTokens](../../x-tokenization/messages/msg-transfer-tokens.md) - Transfer message details
- [IBC Backed Paths](../../x-tokenization/concepts/ibc-backed-paths.md) - How backing works
- [Bot Examples](bot-examples.md) - More agent patterns
- [MCP Builder Tools](mcp-builder-tools.md) - If your agent uses MCP (Claude, etc.)
- [Testnet Faucet](testnet-faucet.md) - Get testnet tokens for testing

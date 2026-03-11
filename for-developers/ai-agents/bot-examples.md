# Bot Examples

Copy-paste examples for common bot and AI agent patterns. All examples use testnet by default.

## Example 1: Mint a Fungible Token

Create a new fungible token collection with server-side signing.

```typescript
import { BitBadgesSigningClient, GenericEvmAdapter, NETWORK_CONFIGS, MsgCreateCollection } from 'bitbadgesjs-sdk';

const adapter = await GenericEvmAdapter.fromMnemonic(
  process.env.BOT_MNEMONIC!,
  NETWORK_CONFIGS['testnet'].evmRpcUrl
);

const client = new BitBadgesSigningClient({
  adapter,
  network: 'testnet'
});

const result = await client.signAndBroadcast([
  MsgCreateCollection.create({
    creator: client.address,
    // ... collection configuration
    // See SDK docs for full MsgCreateCollection fields
  })
]);

if (result.success) {
  console.log('Collection created! TX:', result.txHash);
} else {
  console.error('Failed:', result.error);
}
```

## Example 2: Check Balance & Conditional Transfer

Check a user's token balance and transfer tokens based on a condition.

```typescript
import { BitBadgesAPI, BitBadgesSigningClient, GenericEvmAdapter, NETWORK_CONFIGS, MsgTransferTokens } from 'bitbadgesjs-sdk';

const api = new BitBadgesAPI({ apiUrl: 'https://api.bitbadges.io/testnet' });

// Check balance
const balanceRes = await api.getBalance({
  collectionId: '1',
  address: 'bb1targetaddress...'
});

const balance = balanceRes.balance; // Check specific badge IDs and ownership times

// Conditional transfer
if (/* condition based on balance */) {
  const adapter = await GenericEvmAdapter.fromMnemonic(
    process.env.BOT_MNEMONIC!,
    NETWORK_CONFIGS['testnet'].evmRpcUrl
  );

  const client = new BitBadgesSigningClient({
    adapter,
    network: 'testnet'
  });

  const result = await client.signAndBroadcast([
    MsgTransferTokens.create({
      creator: client.address,
      collectionId: '1',
      transfers: [{
        from: client.address,
        toAddresses: ['bb1targetaddress...'],
        balances: [{
          badgeIds: [{ start: 1n, end: 1n }],
          ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
          amount: 1n
        }]
      }]
    })
  ]);

  console.log('Transfer result:', result.txHash);
}
```

## Example 3: Gate Access (Verify Ownership)

Verify that a user owns a specific badge before granting access.

```typescript
import { BitBadgesAPI } from 'bitbadgesjs-sdk';

const api = new BitBadgesAPI({ apiUrl: 'https://api.bitbadges.io' });

async function checkAccess(userAddress: string): Promise<boolean> {
  try {
    const balanceRes = await api.getBalance({
      collectionId: '5',  // Your gating collection
      address: userAddress
    });

    // Check if user owns badge ID 1
    // The balance object contains detailed ownership info
    const hasAccess = balanceRes.balance.balances.some(b =>
      b.badgeIds.some(range => range.start <= 1n && range.end >= 1n) &&
      b.amount > 0n
    );

    return hasAccess;
  } catch (error) {
    console.error('Ownership check failed:', error);
    return false;
  }
}

// Usage
const allowed = await checkAccess('bb1useraddress...');
if (allowed) {
  // Grant access
} else {
  // Deny access
}
```

## Example 4: Subscribe to Events & Auto-React

Listen for transfers on a specific collection and take action.

```typescript
import WebSocket from 'ws';
import { BitBadgesSigningClient, GenericEvmAdapter, NETWORK_CONFIGS } from 'bitbadgesjs-sdk';

const ws = new WebSocket('wss://rpc-testnet.bitbadges.io/websocket');

ws.on('open', () => {
  // Subscribe to transfer events
  ws.send(JSON.stringify({
    jsonrpc: '2.0',
    method: 'subscribe',
    id: 1,
    params: {
      query: "tm.event='Tx' AND message.action='/badges.MsgTransferTokens'"
    }
  }));
  console.log('Listening for transfers...');
});

ws.on('message', async (data: WebSocket.Data) => {
  const msg = JSON.parse(data.toString());

  if (msg.result?.data?.value?.TxResult) {
    const txHash = msg.result.events?.['tx.hash']?.[0];
    console.log('Transfer detected:', txHash);

    // React to the transfer - e.g., send a notification,
    // update a database, trigger another transaction, etc.
    await handleTransfer(txHash);
  }
});

async function handleTransfer(txHash: string) {
  // Your custom logic here
  console.log(`Processing transfer ${txHash}`);
}

// Reconnection logic
ws.on('close', () => {
  console.log('Disconnected, reconnecting in 5s...');
  setTimeout(() => {
    // Reconnect
  }, 5000);
});
```

## Example 5: MCP Agent Workflow

When using the BitBadges MCP tools (e.g., in Claude Desktop or Claude Code), a typical workflow looks like this:

### Tool Call Sequence

1. **Get instructions** for what you want to build:
   ```
   get_skill_instructions({ skill: "fungible-token" })
   ```

2. **Build the transaction**:
   ```
   build_fungible_token({
     name: "My Bot Token",
     description: "Token managed by my AI agent",
     initialSupply: "1000000",
     ...
   })
   ```

3. **Validate** the transaction is well-formed:
   ```
   validate_transaction({ transactionJson: "<output from step 2>" })
   ```

4. **Simulate** to check gas and catch errors:
   ```
   simulate_transaction({ transactionJson: "<output from step 2>" })
   ```

5. **Sign and broadcast**:
   ```
   sign_and_broadcast({
     transactionJson: "<output from step 2>",
     signingMethod: "mnemonic",
     credential: "<from env>",
     network: "testnet"
   })
   ```

### Query Sequence (No Signing)

```
search({ query: "my collection name" })
  → query_collection({ collectionId: "123" })
  → query_balance({ collectionId: "123", address: "bb1..." })
  → verify_ownership({ collectionId: "123", address: "bb1...", badgeIds: [...] })
```

## Tips for AI Agents

- **Always start on testnet.** The `sign_and_broadcast` MCP tool defaults to testnet for safety. Only switch to mainnet when you're confident in your workflow.
- **Simulate before broadcasting.** Use `simulate_transaction` or the signing client's `simulate: true` option to catch errors before spending gas.
- **Use the faucet for testnet tokens.** See [Testnet Faucet API](testnet-faucet.md) - one request per address, no API key needed.
- **Handle errors gracefully.** Check `result.success` and `result.error` after every broadcast.
- **Sequence management.** The signing client handles nonce/sequence automatically with retries. For MCP tools, `sign_and_broadcast` handles this internally.
- **Store credentials securely.** Use environment variables (`BITBADGES_MNEMONIC`, `BITBADGES_API_KEY`) rather than hardcoding secrets.

# WebSocket Events

BitBadges runs on a Cosmos SDK (CometBFT/Tendermint) blockchain, which exposes a standard WebSocket interface for subscribing to real-time events. This is useful for bots that need to react to on-chain activity like transfers, mints, and collection updates.

## Connection

| Network | WebSocket URL |
|---------|--------------|
| **Mainnet** | `wss://rpc.bitbadges.io/websocket` |
| **Testnet** | `wss://rpc-testnet.bitbadges.io/websocket` |

These are standard CometBFT JSON-RPC WebSocket endpoints.

## Subscribe Method

Use the `subscribe` JSON-RPC method with a Tendermint event query:

```json
{
  "jsonrpc": "2.0",
  "method": "subscribe",
  "id": 1,
  "params": {
    "query": "tm.event='Tx'"
  }
}
```

## Common Queries for Bots

### All Transactions

```
tm.event='Tx'
```

### New Blocks

```
tm.event='NewBlock'
```

### Transactions by Message Type

Subscribe to specific message types (e.g., transfers):

```
tm.event='Tx' AND message.action='/badges.MsgTransferTokens'
```

### Transactions by Sender

```
tm.event='Tx' AND message.sender='bb1abc123...'
```

### Combining Filters

```
tm.event='Tx' AND message.action='/badges.MsgCreateCollection' AND message.sender='bb1abc123...'
```

## Common BitBadges Message Types

| Message Type | Action String |
|-------------|--------------|
| Transfer tokens | `/badges.MsgTransferTokens` |
| Create collection | `/badges.MsgCreateCollection` |
| Update collection | `/badges.MsgUpdateCollection` |
| Delete collection | `/badges.MsgDeleteCollection` |
| Update user approvals | `/badges.MsgUpdateUserApprovals` |
| Create address lists | `/badges.MsgCreateAddressLists` |

## Working Example (Node.js)

```typescript
import WebSocket from 'ws';

const ws = new WebSocket('wss://rpc-testnet.bitbadges.io/websocket');

ws.on('open', () => {
  console.log('Connected to BitBadges testnet WebSocket');

  // Subscribe to all transactions
  ws.send(JSON.stringify({
    jsonrpc: '2.0',
    method: 'subscribe',
    id: 1,
    params: {
      query: "tm.event='Tx'"
    }
  }));
});

ws.on('message', (data: WebSocket.Data) => {
  const msg = JSON.parse(data.toString());

  if (msg.result?.data?.value?.TxResult) {
    const txResult = msg.result.data.value.TxResult;
    const txHash = msg.result.events?.['tx.hash']?.[0];

    console.log('New transaction:', txHash);
    console.log('Height:', txResult.height);

    // Parse events for specific actions
    const events = txResult.result?.events || [];
    for (const event of events) {
      if (event.type === 'message') {
        const action = event.attributes?.find(
          (a: any) => atob(a.key) === 'action'
        );
        if (action) {
          console.log('Action:', atob(action.value));
        }
      }
    }
  }
});

ws.on('error', (err) => {
  console.error('WebSocket error:', err);
});

ws.on('close', () => {
  console.log('Disconnected. Reconnecting...');
  // Implement reconnection logic here
});
```

Install the `ws` package:

```bash
npm install ws
npm install -D @types/ws  # if using TypeScript
```

## Unsubscribe

To stop receiving events:

```json
{
  "jsonrpc": "2.0",
  "method": "unsubscribe",
  "id": 2,
  "params": {
    "query": "tm.event='Tx'"
  }
}
```

Or unsubscribe from all:

```json
{
  "jsonrpc": "2.0",
  "method": "unsubscribe_all",
  "id": 2,
  "params": {}
}
```

## Notes

- Event attributes are base64-encoded in CometBFT responses. Use `atob()` (or `Buffer.from(str, 'base64').toString()`) to decode.
- The WebSocket connection may drop due to network issues. Implement reconnection logic with exponential backoff for production bots.
- The internal BitBadges indexer WebSocket (activity feeds, candlestick data) is not publicly exposed. Use the REST API for indexed data.
- For querying historical data, use the [BitBadges API](../bitbadges-api/) instead of WebSocket events.

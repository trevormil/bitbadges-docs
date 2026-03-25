# Simulation Balance Diffs

Parse chain simulation events into structured balance changes — see exactly what coins and tokens move in each direction before signing.

## Quick Start: simulateAndReview()

```typescript
import { BitBadgesSigningClient } from 'bitbadgesjs-sdk';

const client = new BitBadgesSigningClient({ adapter });
const review = await client.simulateAndReview(messages);

console.log('Gas used:', review.gasUsed);
console.log('Fee:', review.fee);

// See what moves
for (const [address, denoms] of Object.entries(review.netChanges.coinChanges)) {
  for (const [denom, amount] of Object.entries(denoms)) {
    console.log(`${address}: ${amount > 0n ? '+' : ''}${amount} ${denom}`);
  }
}

// If acceptable, broadcast
if (agentApprovesChanges(review.netChanges)) {
  const result = await client.signAndBroadcast(messages);
}
```

## Low-Level: parseSimulationEvents + calculateNetChanges

```typescript
import { parseSimulationEvents, calculateNetChanges } from 'bitbadgesjs-sdk';

// If you have raw simulation events (e.g., from the API)
const parsed = parseSimulationEvents(events, txMessageInfos);
// parsed.coinTransferEvents — coins moving between addresses
// parsed.badgeTransferEvents — badges/tokens moving
// parsed.ibcTransferEvents — IBC transfers

const netChanges = calculateNetChanges(parsed, fee, signerAddress);
// netChanges.coinChanges — per-address coin deltas
// netChanges.badgeChanges — per-address badge deltas
```

## What Events Are Parsed

- Coin transfers (send, delegate, redelegate)
- Mint and burn events
- Badge/token transfers
- IBC transfers
- Protocol fees

## Also Available Via

- **MCP**: The `simulate_transaction` tool returns structured `parsedEvents` and `netChanges`
- **Frontend**: The TxModal "Net Changes" tab uses this under the hood

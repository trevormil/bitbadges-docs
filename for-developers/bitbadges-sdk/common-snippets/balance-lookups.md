# Balance Lookups

BitBadges stores balances as a 3D array where each entry combines an `amount` with ranges of `tokenIds` and `ownershipTimes`. This is powerful but often more than you need. Most use cases just need: "how much of token X does this address hold right now?"

This page covers both flows across every entrypoint.

## Two Ways to Query Balances

| Flow | Returns | Use when |
| --- | --- | --- |
| **Full** | `BalanceArray` — 3D array of `{ amount, tokenIds, ownershipTimes }` | Iterating all holdings, building custom UI, analyzing time-based ownership |
| **Simple** | Single `bigint` amount for one token ID at one point in time | Gating, verification, agent checks — "does this user have token X?" |

---

## SDK

### Full flow

```typescript
const res = await api.getCollections({
  collectionsToFetch: [{ collectionId: '1' }]
});
const balances = res.collections[0].getBalances('bb1...');
// Returns: BalanceArray<bigint> — 3D array with amounts, tokenIds, ownershipTimes
```

### Simple flow

```typescript
import { getBalanceForIdNow, getBalanceForIdAndTime } from 'bitbadgesjs-sdk';

// Get balance for token 5 right now
const amount = getBalanceForIdNow(5n, balances);

// Get balance for token 5 at a specific time (milliseconds)
const historicalAmount = getBalanceForIdAndTime(5n, 1700000000000n, balances);

// Or use the collection method directly
const amount = collection.getBalanceAmountForToken('bb1...', 5n);
```

---

## API

### Full flow

```
POST /api/v0/collection/:collectionId/balance/:address
```

Returns the full balance document including the 3D balance array and approvals.

### Simple flow

```
GET /api/v0/collection/:collectionId/:tokenId/balance/:address
GET /api/v0/collection/:collectionId/:tokenId/balance/:address?time=1700000000000
```

Returns a single amount. Defaults to the current time if `time` is omitted.

```json
{ "balance": "100" }
```

---

## MCP Tools

### Full flow

```
query_balance({ collectionId: "1", address: "bb1..." })
```

Returns the full 3D balance array.

### Simple flow

```
query_balance({ collectionId: "1", address: "bb1...", tokenId: "5" })
```

Returns a flat object with the resolved amount:

```json
{ "balance": "100", "tokenId": "5", "collectionId": "1", "address": "bb1..." }
```

---

## Chain CLI

```bash
bitbadgeschain query balance [collection-id] [address]
```

Returns the full balance object. There is no simplified CLI variant — use the API or SDK simple flow instead.

---

## When to Use Which

* **Simple flow** — "Does this user have token X?" This is the most common case for agents, gating, and verification.
* **Full flow** — You need to iterate all holdings, build a custom UI, or analyze time-based ownership history.

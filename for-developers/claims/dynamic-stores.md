# Dynamic Stores

Dynamic stores are serverless address lists hosted by BitBadges. You create a store, and BitBadges stores and manages the list for you. You add and remove addresses via API calls — from anywhere: your backend, a Zapier automation, an LLM agent, a cron job, or any system that can make HTTP requests. The store is then attached to claims as a gating mechanism.

The core idea: **decouple eligibility management from claim configuration.** Instead of hardcoding who can claim, maintain a living list that updates in real-time as your business logic dictates.

## How It Works

1. **Create a store** in the developer portal → receive a store ID and secret
2. **Add/remove addresses** via API whenever your eligibility criteria change
3. **Attach the store** to one or more claims using the `whitelist` plugin
4. **Users claim** — BitBadges checks if the user's address is in the store

Stores are reusable — attach one store to multiple claims. Update the store once, and all claims using it reflect the change.

## Queue-Based Processing

Store actions (add/remove) are processed through a queue system. When you call the API, the action is queued and processed asynchronously. There may be a **1-2 second delay** between when you trigger an action and when it's finalized in the store.

This means:
- If you add an address and the user immediately tries to claim, the store may not have updated yet
- For time-sensitive flows, build in a small buffer or retry logic on the user side
- Batch actions are processed together, so they're consistent relative to each other

## Creating a Store

Create stores in the [developer portal](https://bitbadges.io/developer). You receive:

- **Store ID** (`dynamicDataId`) — identifies the store in API calls and claim configuration
- **Store secret** (`dataSecret`) — authenticates API requests (keep this secret on your backend)

## Managing Entries

### Via UI

Manage entries directly in the developer portal — add, remove, and view addresses.

### Via API — Single Action

```typescript
// POST /api/v0/storeActions/single
await BitBadgesApi.performStoreAction({
  dynamicDataId: 'your-store-id',
  dataSecret: 'your-store-secret',  // Omit if signed in as creator
  actionName: 'add',                // 'add' or 'remove'
  payload: {
    address: 'bb1...'
  }
});
```

### Via API — Batch Actions

```typescript
// POST /api/v0/storeActions/batch
await BitBadgesApi.performBatchStoreAction({
  dynamicDataId: 'your-store-id',
  dataSecret: 'your-store-secret',
  actions: [
    { actionName: 'add', payload: { address: 'bb1abc...' } },
    { actionName: 'add', payload: { address: 'bb1def...' } },
    { actionName: 'remove', payload: { address: 'bb1xyz...' } }
  ]
});
```

### API Payload Reference

```typescript
interface iPerformStoreActionSingleWithBodyAuthPayload {
  _isSimulation?: boolean;     // Dry-run mode
  dynamicDataId: string;       // Store ID
  dataSecret?: string;         // Auth secret (omit if signed in as creator)
  actionName: string;          // 'add' | 'remove'
  payload: {
    address: string;           // BitBadges address to add/remove
  };
}

interface iPerformStoreActionBatchWithBodyAuthPayload {
  _isSimulation?: boolean;
  dynamicDataId: string;
  dataSecret?: string;
  actions: {
    actionName: string;
    payload: { address: string };
  }[];
}
```

## Data Model

```typescript
interface iDynamicDataDoc {
  handlerId: 'addresses';       // Only "addresses" is supported
  dynamicDataId: string;        // Unique store ID
  label: string;                // Display label
  dataSecret: string;           // Authentication secret
  data: string[];               // The stored addresses
  createdBy: BitBadgesAddress;
  managedBy: BitBadgesAddress;
  publicUseInClaims?: boolean;  // If true, anyone can attach this store to a claim
  createdAt?: number;           // UNIX ms
  lastUpdated?: number;         // UNIX ms
}
```

## Authentication

- **Signed in as creator/manager:** No `dataSecret` needed
- **API access:** Include `dataSecret` in the request body
- **Public stores:** If `publicUseInClaims` is true, anyone can reference the store in claims, but management (add/remove) still requires authentication

## Attaching to Claims

Use the `whitelist` plugin with dynamic store configuration:

```typescript
{
  pluginId: 'whitelist',
  instanceId: 'my-whitelist',
  publicParams: {
    maxUsesPerAddress: 1
  },
  privateParams: {
    useDynamicStore: true,
    dynamicDataId: 'your-store-id',
    dataSecret: 'your-store-secret'
  }
}
```

Or use the claim builder UI: select your store from the templates section, or navigate to the store's page and click "Create Claim."

## Integration Patterns

The API-driven design means you can trigger add/remove from **any system** that can make HTTP requests:

**Backend webhook:**
```typescript
// Shopify purchase → add buyer to eligible list
app.post('/webhooks/shopify-purchase', async (req, res) => {
  const buyerAddress = await lookupAddress(req.body.customer.email);
  await BitBadgesApi.performStoreAction({
    dynamicDataId: 'eligible-buyers',
    dataSecret: process.env.STORE_SECRET,
    actionName: 'add',
    payload: { address: buyerAddress }
  });
  res.sendStatus(200);
});
```

**AI agent / LLM:**
```typescript
// AI evaluates eligibility and populates the store
const eligible = await yourAIModel.evaluate(userAddress);
if (eligible) {
  await BitBadgesApi.performStoreAction({
    dynamicDataId: 'ai-eligible',
    dataSecret: process.env.STORE_SECRET,
    actionName: 'add',
    payload: { address: userAddress }
  });
}
```

**Zapier / no-code automation:**
- Use Zapier's webhook action to call the BitBadges API
- Trigger from any Zapier-supported event (form submission, CRM update, calendar event, etc.)
- No code required — just configure the HTTP request in Zapier

**Cron job / scheduled task:**
```typescript
// Daily job: sync eligible users from your database
const eligibleUsers = await db.query('SELECT address FROM users WHERE eligible = true');
await BitBadgesApi.performBatchStoreAction({
  dynamicDataId: 'daily-eligible',
  dataSecret: process.env.STORE_SECRET,
  actions: eligibleUsers.map(u => ({
    actionName: 'add',
    payload: { address: u.address }
  }))
});
```

**Custom plugin (success webhook):**
A custom plugin can add addresses to a store as a side effect of claim success — effectively chaining claims together.

## Limitations

- Off-chain stores can only be used in **off-chain contexts** (claims via the `whitelist` plugin). They are not accessible on-chain.
- Only **addresses** are supported as identifiers (no emails, usernames, etc.)
- Queue processing introduces a **1-2 second delay** between API call and store update

---

## On-Chain Dynamic Stores

BitBadges also supports on-chain dynamic stores — boolean address-value stores on the blockchain that integrate with approval criteria via `DynamicStoreChallenge`. These are separate from the off-chain stores described above and are managed via blockchain transactions, not the API. Similar concepts, just on vs off-chain.

See [Dynamic Store Challenges](../../token-standard/learn/approval-criteria/dynamic-store-challenges.md) and [MsgCreateDynamicStore](../../x-tokenization/messages/msg-create-dynamic-store.md) for full documentation.

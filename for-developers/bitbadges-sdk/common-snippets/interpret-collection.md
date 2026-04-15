# Collection Interpreter

The `interpretCollection` function generates a thorough, human-readable explanation of any collection — permissions, approvals, claims, invariants, metadata, and risks. It works with the full `BitBadgesCollection` object including metadata and claims.

## Usage

```typescript
import { interpretCollection } from 'bitbadges';
import { BitBadgesApi } from 'bitbadges';

// After fetching a collection
const collection = await BitBadgesApi.getCollections({
  collectionsToFetch: [{ collectionId: '1' }]
});
const explanation = interpretCollection(collection.collections[0]);
console.log(explanation);
```

## What's Included in the Output

- **Collection overview** — name, type, token range, standards
- **How tokens are created/obtained** — every mint approval explained
- **Transferability rules** — every transfer approval explained
- **Default balances** and auto-approve settings
- **What the manager can and cannot change** — every permission's state
- **On-chain invariants and guarantees**
- **Claims** — plugin types, flow, rewards (no secrets)
- **IBC/cross-chain details**

## Privacy

The function automatically strips sensitive claim parameters (`seedCode`, `preimages`, private params) from the output. Safe to display to any user.

## Also Available Via

- **Builder**: The `explain_collection` tool uses this under the hood
- **Frontend**: Displayed on collection overview pages

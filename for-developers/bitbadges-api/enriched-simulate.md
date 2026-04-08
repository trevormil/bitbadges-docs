# Enriched Simulate & Collection Review

The simulate endpoint returns additional analysis alongside gas estimation. A separate review endpoint provides the same analysis for collection JSON without chain simulation.

## Enriched Simulate Response

`POST /api/v0/simulate` returns the standard Cosmos simulation response plus three additional fields:

```json
{
  "gas_info": { "gas_wanted": "...", "gas_used": "..." },
  "result": { "events": [...] },

  "validationErrors": [...],
  "auditFindings": [...],
  "standardsCompliance": [...]
}
```

### validationErrors

Structural issues found by `validateTransaction()`. These are problems that would likely cause the transaction to fail or behave unexpectedly.

### auditFindings

Security and design warnings from `auditCollection()`. These are advisory — they highlight risky patterns (overly permissive approvals, missing permissions locks, etc.) but do not cause transaction failure.

### standardsCompliance

Results from `verifyStandardsCompliance()`. If the collection declares standards (e.g., fungible token, NFT), this checks whether the configuration actually meets those standards.

Each field is `null` if the corresponding check could not run (e.g., the transaction type does not contain collection data).

## Review Collection Endpoint

`POST /api/v0/review-collection` runs the same three checks on a collection JSON without simulating against the chain. Used by `bitbadges-cli sdk review <file>` for collection files.

### Request

```json
{
  "collection": {
    "collectionMetadataTimeline": [...],
    "collectionApprovals": [...],
    ...
  }
}
```

### Response

```json
{
  "validationErrors": [...],
  "auditFindings": [...],
  "standardsCompliance": [...]
}
```

## Review Items (Chain Message Responses)

Every tokenization module message response includes a `reviewItems` field — an array of advisory strings highlighting important aspects of what the transaction did.

```json
{
  "reviewItems": [
    "This transfer used approval 'public-mint' (version 3) which has coin transfer side effects",
    "Approval 'presale' was deleted because it had autoDeletion enabled and reached its max uses",
    "Collection approval permissions are permanently locked"
  ]
}
```

Review items are:
- **Advisory only** — they never cause a transaction to fail
- Returned in the message response, not emitted as events
- Useful for wallets, frontends, and AI agents to understand what happened

## SDK Access

### BitBadgesSigningClient

```typescript
const review = await client.simulateAndReview(messages);
// review includes enrichment fields when available
```

### CLI

```bash
bitbadges-cli sdk review tx.json
# Calls enriched simulate if BITBADGES_API_KEY is set
```

### MCP

The `simulate_transaction` tool returns structured `parsedEvents` and `netChanges` alongside the enrichment fields.

## See Also

- [Simulation Balance Diffs](../bitbadges-sdk/common-snippets/simulation-balance-diffs.md) — parsing transfer events from simulation
- [Approval Change Events](../../x-tokenization/concepts/approval-change-events.md) — chain events emitted on approval changes
- [CLI Reference](../bitbadges-sdk/cli.md) — `sdk review` command

# Refresh / Claim Completion Queue

The API / indexer makes use of a load-balanced refresh queue system whenever we need to fetch anything from a source URI (metadata, off-chain balances, etc). Because this is a queue-based system, certain metadata may take awhile to fully load and populate. Once we fetch the metadata, we cache it and return the fetched values until it is refreshed again.

**When do we trigger refreshes?**

Refreshes are triggered automatically when certain things occur on-chain, such as a collection is created / URI is changed. You can also manually trigger refreshes (note there are cooldown limits in place to prevent spam) to refresh the cached values via the refresh endpoints.

**What happens if the fetch fails?**

See [Restrictions / Limits](limits-restrictions.md). We implement an exponential retry system.

**Off-Chain Balances**

Note that for off-chain balances, we also throw an error if the fetched balances exceed the total supply of tokens defined on-chain (i.e. you are trying to allocate more tokens than you should).

**Checking Status**

If you are having issues, you can check the BitBadges collection page on site -> Actions -> Refresh for statuses. Or, if you need a programmatic solution, you can use following route to see its status and see if it has any error docs.

```typescript
await BitBadgesApi.getRefreshStatus()
```


# Refresh Queue

Whenever we need to fetch anything from a source URI (metadata, off-chain balances, etc), we do so via a load-balanced refresh queue system. Because this is a queue-based system, certain metadata may take awhile to fully load and populate. Once we fetch the metadata, we cache it and return the fetched values until it is refreshed again.

**When do we trigger refreshes?**

Refreshes are triggered automatically when a collection is created / URI is changed. Whenever the transaction is processed by the indexer, we automatically add it to the queue and fetch as soon as we can.

You can also manually trigger refreshes (limit once per minute) to refresh the cached values via the refresh endpoint.&#x20;

**What happens if the fetch fails?**

See [Restrictions / Limits](../limits-restrictions.md).

**Off-Chain Balances**

Note that for off-chain balances, we also throw an error if the fetched balances exceed the total supply of badges defined on-chain (i.e. you are trying to allocate more badges than you should).

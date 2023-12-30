# Limits / Restrictions

Note that the following limits and restrictions are in place for the API. Reach out to us if these limits are too restrictive. These can change and the values below may be outdated.

* 250 unique metadata URIs, accounts, collections fetched in one request
* Functionality may be limited for collections with more than JavaScript's Number.MAX\_SAFE\_INTEGER unique badges.
* Refreshes can only be executed once every hour.
* Fetches directly from source URI that take >= 10 seconds will timeout.
* If a fetch from source fails, we implement an exponential retry policy:&#x20;
  * 1 hour \* 2^(Number of Attempted Fetches)
* Total IPFS uploads per address cannot exceed 100MB cumulatively.
* Global rate limit in place to prevent infinite loops. See limiter value [here](https://github.com/BitBadges/bitbadges-indexer/blob/master/src/indexer.ts).
* 10000 requests per day per API key.

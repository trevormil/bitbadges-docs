# Limits / Restrictions

Note that the following limits and restrictions are in place for the API. Reach out to us if you need additional support, or run your own indexer!

* 250 unique metadata URIs, accounts, collections fetched in one request
* Functionality may be limited for collections with more than JavaScript's Number.MAX\_SAFE\_INTEGER unique badges.
* Refreshes can only be executed once every 60 seconds.
* Fetches directly from source URI that take >= 30 seconds throw an error.
* If a fetch from source fails, we implement an exponential retry policy.
  * We wait a minimum of 1 hour \* 2^(Number of Attempted Fetches)
* Total IPFS uploads per address cannot exceed 100MB cumulatively.
* Rate limit in place to prevent infinite loops. See limiter value [here](https://github.com/BitBadges/bitbadges-indexer/blob/master/src/indexer.ts).
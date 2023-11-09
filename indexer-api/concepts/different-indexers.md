# Different Indexers

As mentioned, the indexer is open-source, and anyone can run their own indexer to customize the data that they want to store.

All indexers will be able to query the blockchain and obtain all information stored on the blockchain. **However, different, disconnected indexers will not be able to access each others' databases and private features (airdrop statuses, secret codes, etc).** Indexers are considered disconnected, if not replicated with each other via CouchDB (see [https://couchdb.apache.org/](https://couchdb.apache.org/)).

For example, if I create a profile and store my Twitter via the official BitBadges indexer, this is only stored off-chain via that indexer's database. It will not be stored in your indexer's database, if you choose to run your own.

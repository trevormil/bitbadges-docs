# Overview

Storing all possible data that may be needed for a project directly on the blockchain, such as all activity or metadata, would get expensive very quickly. Enter the BitBadges indexer!

The BitBadges indexer runs in tandem with a blockchain node and is customizable to store and serve any data that a project may need via an Express.js API.&#x20;

GitHub: [https://github.com/bitbadges/bitbadges-indexer](https://github.com/bitbadges/bitbadges-indexer)

**How It Works**

Every second, the indexer will query the latest transactions added to the blockchain and index the data in a customizable manner.&#x20;

For example, if a new MsgTransferBadge transaction is observed, the indexer can add this to an activity array stored for that user. All activity for a specific user can then be easily fetched via the Express.js API. This makes certain data easily accessible and queryable, even though it isn't stored like that on the blockchain.

**Running Your Own Indexer vs Using the BitBadges API**

The indexer code is open-source, so feel free to run your own indexer and customize it as needed for your project. See [Indexer](indexer.md).

However, we run an official BitBadges Indexer and API which should provide access to the necessary data required for most projects. See [API](api.md) for the data provided by the BitBadges API.

# Indexer

If you want to run your own indexer and API, check out the source code at [https://github.com/bitbadges/bitbadges-indexer](https://github.com/bitbadges/bitbadges-indexer).

The indexer is split into two main parts: the poller and the API. The poller fetches the latest block from a connected node every second and updates the MongoDB database accordingly. The API is an Express.js API that makes the indexed data queryable to users.

Although you can query other blockchain nodes, it is strongly recommended that you run your own node and query that. HTTP requests can reach >100 per second.

### Running from Scratch

1. Install and setup MongoDB
2. Setup a valid .env file. See environment.d.ts below for the expected format of the .env file.&#x20;
3. Use **npm run setup** to setup the CouchDB databases. Note that you can run **npm run setup with-delete** to restart all indexer databases from scratch.
4. Use **npm run indexer-dev** to start in development mode.
5. Use **npm run build** and **npm run indexer** to start in production mode.

### Running with Docker

See [https://github.com/bitbadges/bitbadges-docker](https://github.com/bitbadges/bitbadges-docker).

### Customization Options (.env file)

Below are the supported customization options. Some may be applicable. Some may not.

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-indexer/blob/master/environment.d.ts" %}


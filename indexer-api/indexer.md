# Indexer

If you want to run your own indexer and API, check it out at [https://github.com/bitbadges/bitbadges-indexer](https://github.com/bitbadges/bitbadges-indexer).

The indexer is split into two main parts: the poller and the API. The poller fetches the latest blockchain info from a connected node every second and updates the CouchDB database accordingly. The API is an Express.js API that makes the indexed data queryable to users.

Note that CouchDB uses an [optimistic conflict resolution system](https://docs.couchdb.org/en/stable/replication/conflicts.html). If you add functionality to the indexer, design it with this in mind.

### Running from Scratch

1. Install CouchDB
2. Start a BitBadges blockchain node
3. Setup a valid .env file. See environment.d.ts for the expected format of the .env file.
4. Use **npm run setup** to setup the CouchDB databases. Note that this wipes everything to initial state, so do not use this command if you already have progress saved. Only use this command on initial setup or when you want to reset entirely.
5. Use **npm run indexer-dev** to start in development mode.
6. Use **npm run build** and **npm run indexer** to start in production mode.

### Running with Docker

[https://github.com/bitbadges/bitbadges-docker](https://github.com/bitbadges/bitbadges-docker)

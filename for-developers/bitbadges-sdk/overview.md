# Overview

The BitBadges SDK is a bundle of TypeScript libraries that provide all the tools and functions needed for you to build your own frontend or interact with the BitBadges API, blockchain, and indexer.

GitHub: [https://github.com/bitbadges/bitbadgesjs](https://github.com/bitbadges/bitbadgesjs)

See [full documentation](full-documentation.md) for complete documentation on each library.

```
npm install bitbadgesjs-sdk
```

This library provides miscellaneous functionality to help you interact with BitBadges, such as types, API routes, managing metadata requests, logic with ID ranges and balances, etc.

```typescript
const cosmosAddress = convertToCosmosAddress(address);
const ethAddress = cosmosToEth(cosmosAddress);
```

It also exports functions for broadcasting transactions and interacting with the blockchain. See [Broadcasting Txs](../create-and-broadcast-txs/) for how to use.

```typescript
// Find a node URL from a network endpoint:
// https://docs.evmos.org/develop/api/networks.
const nodeUrl = ...

const postOptions = {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: generatePostBodyBroadcast(signedTx),
}

const broadcastEndpoint = `${nodeUrl}${generateEndpointBroadcast()}`
const broadcastPost = await fetch(
  broadcastEndpoint,
  postOptions,
)

const response = await broadcastPost.json()
```

For most use cases, you will not need to broadcast transactions. If you do, consider first exploring the helper broadcast tool at [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast).

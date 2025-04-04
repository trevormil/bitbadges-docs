# Overview

The BitBadges SDK is a bundle of TypeScript libraries that provide all the tools and functions needed for you to build your own frontend or interact with the BitBadges API, blockchain, and indexer.

GitHub: [https://github.com/bitbadges/bitbadgesjs](https://github.com/bitbadges/bitbadgesjs)

Full Documentation: [https://bitbadges.github.io/bitbadgesjs/](https://bitbadges.github.io/bitbadgesjs/)

```
npm install bitbadgesjs-sdk
```

Note: We also have a python API SDK wrapper. This only includes the API routes auto-generated and no helper functions like the SDK route.

```
pip install bitbadgespy-sdk
```

This library provides miscellaneous functionality to help you interact with BitBadges, such as types, API routes, managing metadata requests, logic with ID ranges and balances, etc.

```typescript
const bitbadgesAddress = convertToBitBadgesAddress(address);
const ethAddress = bitbadgesToEth(bitbadgesAddress);
```

It also exports functions for broadcasting transactions and interacting with the blockchain. See [Broadcasting Txs](../bitbadges-blockchain/create-and-broadcast-txs/) for how to use.

```typescript
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

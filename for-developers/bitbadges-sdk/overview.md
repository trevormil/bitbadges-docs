# Overview

The BitBadges SDK is a bundle of TypeScript libraries that provide all the tools and functions needed for you to build your own frontend or interact with the BitBadges API, blockchain, and indexer.&#x20;

GitHub: [https://github.com/bitbadges/bitbadgesjs](https://github.com/bitbadges/bitbadgesjs)

Reference repositories that use the SDK:

* [BitBadges Frontend](https://github.com/BitBadges/bitbadges-frontend)
* [BitBadges Indexer / API](https://github.com/BitBadges/bitbadges-indexer)

See [full documentation](full-documentation.md) for complete documentation on each library. Also, the BitBadges official indexer source code and BitBadges official frontend code both use the SDK, so please feel free to reference them.

```
npm install bitbadgesjs-sdk
```




This library provides miscellaneous functionality to help you interact with BitBadges, such as types, API routes, managing metadata requests, logic with ID ranges and balances, etc.&#x20;

```typescript
const idRangesOverlap = checkIfIdRangesOverlap(balances[0].badgeIds);
```

```typescript
const metadata = updateMetadataMap(metadata, currentMetadata, { start: badgeId, end: badgeId }, uri);
```

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

The **proto** portion of the library exports the Protocol Buffer types for everything needed to interact with the chain, API, and indexer. Use this library to create, generate, and sign blockchain transactions in the [BitBadges Msg formats](../create-and-broadcast-txs/cosmos-sdk-msgs/). See [Broadcasting Txs](../create-and-broadcast-txs/) for more info and tutorials.&#x20;

For most use cases, you will not need to broadcast transactions. If you do, consider first exploring the helper broadcast tool at [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast).

```typescript
const txMsg = createTxMsgUpdateCollection(
    txDetails.chain,
    txDetails.sender,
    txDetails.fee,
    txDetails.memo,
    msg
)
```

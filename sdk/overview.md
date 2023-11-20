# Overview

The BitBadges SDK is a TypeScript library that provides all the tools and functions needed for you to interact with the BitBadges API, blockchain, indexer, or build your own frontend.&#x20;

GitHub: [https://github.com/bitbadges/bitbadgesjs](https://github.com/bitbadges/bitbadgesjs).

There are currently four libraries that make up the SDK (address-converter, proto, provider, utils) that can be installed via the commands below. See [full documentation](full-documentation.md) for complete documentation on each library. Also, the BitBadges official indexer source code and BitBadges official frontend code both use the SDK, so please feel free to reference them.

```
npm install bitbadgesjs-address-converter
```

```
npm install bitbadgesjs-provider
```

```
npm install bitbadgesjs-utils
```

```
npm install bitbadgesjs-proto
```



**utils** is the main library which provides miscellaneous functionality to help you interact with the BitBadges API and indexer, such as types, API routes, managing metadata requests, logic with ID ranges and balances, etc.

```typescript
const idRangesOverlap = checkIfIdRangesOverlap(balances[0].badgeIds);
```

```typescript
const metadata = updateMetadataMap(metadata, currentMetadata, { start: badgeId, end: badgeId }, uri);
```



**address-converter** allows you to switch between equivalent addresses of different ecosystems.

```typescript
const cosmosAddress = ethToCosmos(address);
const ethAddress = cosmosToEth(cosmosAddress);
```

**proto** exports the Protocol Buffer types for everything needed to interact with the chain, API, and indexer. Use this library to create blockchain transactions in the [BitBadges Msg formats](../for-developers/cosmos-sdk-msgs/). See [Broadcasting Txs](broadcasting-and-signing-txs.md) for more info and tutorials.&#x20;

```typescript
const txMsg = createTxMsgUpdateCollection(
    txDetails.chain,
    txDetails.sender,
    txDetails.fee,
    txDetails.memo,
    msg
)
```

**provider** exports functions for broadcasting transactions and interacting with the blockchain. See [Broadcasting Txs](broadcasting-and-signing-txs.md) for how to use.

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

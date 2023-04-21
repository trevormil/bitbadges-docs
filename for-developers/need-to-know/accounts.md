# 👤 Accounts

**How is BitBadges able to support addresses from different blockchains?**

Addresses from each individual L1 blockchain will be mapped to a) an equivalent Cosmos address and b) an account ID number. The account ID number will be assigned the first time an address interacts with the BitBadges blockchain or is sent $BADGE.

For example, the Ethereum zero address 0x0000000000000000000000000000000000000000 maps to an equivalent Cosmos address cosmos1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a. When this address first interacts with the BitBadges blockchain, it will be assigned its account ID number.&#x20;

**Which address should I use (native or mapped Cosmos one)? How to convert?**

For user experience, you should always display the user's native address on the frontend. However, the BitBadges blockchain will only use Cosmos addresses and account IDs, never the original address. This should be converted behind the scenes using the **convertFunctions** from [BitBadges SDK](broken-reference).

We typically refer to the native address as **address** and the corresponding Cosmos address as **cosmosAddress**.

```typescript
const cosmosAddress = ethToCosmos(address);
const ethAddress = cosmosToEth(cosmosAddress);
```

**What if the user has not interacted / registered with the BitBadges blockchain yet?**

If a user has not interacted with the blockchain yet, they will not have been assigned an account ID number yet.&#x20;

For view-only purposes, an account ID number should not be needed. However, for interacting with the blockchain via a transaction which involves that user, you will need them to register and be assigned an account ID number.

**How to register an address (i.e. assign them an account ID number)?**

You can register addresses a) by creating a [MsgRegisterAddresses](tx-msg-interfaces.md) transaction and [broadcasting it](../../sdk/broadcasting-and-signing-txs.md) to the blockchain (this registers address on their behalf but the transaction sender pays gas fee), b) sending them any amount of $BADGE, or c) having them claim their airdrop from the faucet (if airdrop window is open).

**How do I query the account ID number for an address?**

To get this information, you have to query it. If you are using the BitBadges API, you can use [these routes](../../use-cases/use-cases.md). To query the blockchain directly, you can use the following [REST API route](https://docs.cosmos.network/v0.46/run-node/interact-node.html):

```
GET /​cosmos/​auth/​v1beta1/​accounts/​cosmos1abcd...xyz
```

Note that account IDs are permanent and will not change, so you can cache them if you would like.

You can also check out how [https://github.com/BitBadges/bitbadges-indexer](https://github.com/BitBadges/bitbadges-indexer) queries such information using CosmJS in the /src/chain-client directory.&#x20;

**Burn Address**

The Ethereum burn address is programmed to be the first account to be assigned an account ID number. Thus, the address 0x0000000000000000000000000000000000000000 or cosmos1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a will have an account ID of 1. This is to be used as the burn address for badges.

# 👤 Accounts

**How is BitBadges able to support addresses from different blockchains?**

To enable interoperability between different blockchains, each individual L1 blockchain will have its native addresses mapped to an equivalent Cosmos address and an account ID number. An account ID number will be assigned to the address when it interacts with the BitBadges blockchain or is sent $BADGE for the first time.&#x20;

As an example, the Ethereum zero address 0x0000000000000000000000000000000000000000 maps to the Cosmos address cosmos1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a and will be assigned an account ID number upon interaction with the BitBadges blockchain.

We typically refer to the native address as **address** and the corresponding Cosmos address as **cosmosAddress**. For Cosmos-native addresses, **address** will be the same as **cosmosAddress.**

**Which address should I use (native or mapped Cosmos one)? How to convert?**

For user experience, you should always display the user's native address on the frontend. However, the BitBadges blockchain only uses Cosmos addresses and account ID numbers, never a native address. This should be converted behind the scenes using the convert function from [BitBadges SDK](broken-reference) (address-converter).

<pre class="language-typescript"><code class="lang-typescript"><strong>import { ethToCosmos, cosmosToEth } from 'bitbadgesjs-address-converter';
</strong><strong>
</strong><strong>const cosmosAddress = ethToCosmos(address);
</strong>const ethAddress = cosmosToEth(cosmosAddress);
</code></pre>

**What if the user has not interacted / registered with the BitBadges blockchain yet?**

If a user has not interacted with the blockchain yet, they will not have been assigned an account ID number yet.&#x20;

For view-only purposes, an account ID number should not be needed. However, for interacting with the blockchain, you will most likely need an account ID number and need to register them.

**How to register an address?**

There are three ways to register addresses in BitBadges. First, users can register an address through a [MsgRegisterAddresses](msgs.md) transaction, which allows the transaction sender to register any address(es). Second, sending any amount of $BADGE to an address can also register it. Third, addresses can claim their airdrop from the faucet during the airdrop window.

**How do I query the account ID number for an address?**

To get this information, you have to query it, which can be done in a few different ways.

1. If you are using the BitBadges API, you can use [these routes](../../use-cases/use-cases.md). (recommended option)
2. To query a node directly, you can use the following [REST API route](https://docs.cosmos.network/v0.46/run-node/interact-node.html):

```
GET /​cosmos/​auth/​v1beta1/​accounts/​cosmos1abcd...xyz
```

3. You can also check out how [https://github.com/BitBadges/bitbadges-indexer](https://github.com/BitBadges/bitbadges-indexer) queries such information using CosmJS in the /src/chain-client directory.&#x20;

**Are account IDs permanent?**

Yes, account IDs are permanent and will not change. You can cache them if you would like.

**Burn Address**

The Ethereum burn address is programmed to be the first account to be assigned an account ID number. Thus, the address 0x0000000000000000000000000000000000000000 which maps to cosmos1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a will have an account ID number of 1. This is to be used as the burn address for badges.

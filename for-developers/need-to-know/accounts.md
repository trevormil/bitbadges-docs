# 👤 Accounts

This page will give you an overview of BitBadges accounts. It should be enough information for most, but for more low-level interaction, [the next page](accounts-technical.md) will give you more in-depth explanations.&#x20;



**How is BitBadges able to support addresses from different blockchains?**

To enable interoperability between different blockchains, each individual L1 blockchain will have its native addresses mapped to an equivalent Cosmos address and an account ID number. An account ID number will be assigned to the address when it interacts with the BitBadges blockchain or is sent $BADGE for the first time.&#x20;

As an example, the Ethereum zero address 0x0000000000000000000000000000000000000000 maps to the Cosmos address cosmos1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a and will be assigned an account ID number upon interaction with the BitBadges blockchain.

We typically refer to the native address as **address** and the corresponding Cosmos address as **cosmosAddress**. For Cosmos-native addresses, **address** will be the same as **cosmosAddress.**

**Which address should I use (native or mapped Cosmos one)? How to convert?**

For user experience, you should always display the user's native address on the frontend. However, the BitBadges blockchain only uses Cosmos addresses, never a native address. This should be converted behind the scenes using the converter functions from [BitBadges SDK](broken-reference) (address-converter). This can be done with any validly formatted address.

<pre class="language-typescript"><code class="lang-typescript"><strong>import { ethToCosmos, cosmosToEth } from 'bitbadgesjs-address-converter';
</strong><strong>
</strong><strong>const cosmosAddress = ethToCosmos(address);
</strong>const ethAddress = cosmosToEth(cosmosAddress);
</code></pre>

**How do I query information for an address, such as balance or sequence (nonce)?**

To get this information, you have to query it, which can be done in a few different ways.

1. If you are using the BitBadges API, you can use [these routes](../../overview/use-cases.md). (recommended option)
2. To query a node directly, you can use the Cosmos [REST API ](https://docs.cosmos.network/v0.46/run-node/interact-node.html)routes.
3. You can also check out how [https://github.com/BitBadges/bitbadges-indexer](https://github.com/BitBadges/bitbadges-indexer) queries such information using CosmJS in the /src/chain-client directory.&#x20;

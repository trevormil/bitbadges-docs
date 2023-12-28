# 👤 Accounts

This page will give you an overview of BitBadges accounts. It should be enough information for most, but for more low-level interaction, [the next page](accounts-technical.md) will give you more in-depth explanations.&#x20;

#### **How is BitBadges able to support addresses from different blockchains?**

To enable interoperability between different blockchains, each individual L1 blockchain will have its native addresses mapped to an equivalent Cosmos address and an account ID number. An account ID number will be assigned to the address when it interacts with the BitBadges blockchain or is sent $BADGE for the first time.&#x20;

As an example, the Ethereum null address 0x0000000000000000000000000000000000000000 maps to the Cosmos address cosmos1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a and will be assigned an account ID number upon interaction with the BitBadges blockchain.

#### **Representation** <a href="#addresses-and-public-keys" id="addresses-and-public-keys"></a>

Ethereum Example:

* Address (Bech32): `cosmos14au322k9munkmx5wrchz9q30juf5wjgz2cfqku`
* Address ([EIP55](https://eips.ethereum.org/EIPS/eip-55) Ethereum Hex): `0xAF79152AC5dF276D9A8e1E2E22822f9713474902`

Solana Example:

* Address (Base58): 6H2af68Yyg6j7N4XeQKmkZFocYQgv6yYoU3Xk491efa5
* Address (Bech32): cosmos18el5ug46umcws58m445ql5scgg2n3tzat53tsw

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

#### **Which address should I use (native or mapped Cosmos one)? How to convert?**

For user experience, you should always display the user's native address on a frontend. However, the BitBadges blockchain **only** uses the mapped Cosmos addresses behind the scenes, never a native address. This can be converted behind the scenes using the converter functions from [BitBadges SDK](../bitbadges-sdk/) (address-converter). This can be done with any validly formatted address.

<pre class="language-typescript"><code class="lang-typescript"><strong>import { ethToCosmos, cosmosToEth } from 'bitbadgesjs-utils';
</strong><strong>
</strong><strong>const cosmosAddress = ethToCosmos(address);
</strong>const ethAddress = cosmosToEth(cosmosAddress);
const cosmosAddressFromSolana = solanaToCosmos(address);
//Note there is no cosmosToSolana or ethToSolana due to how the addresses work
</code></pre>

#### Why can I convert Solana address to a  Cosmos / Eth address but not the other way around?

You may notice that you cannot from a Cosmos / Eth address directly to a Solana address. This is because conversion from a Solana address requires a hash, so if you just have the postimage of the hash (an Eth / Cosmos address), you cannot deduce the preimage without prior knowledge of it.

#### **How do I query details for an address?**

1. You can use the [BitBadges API](../bitbadges-api/api.md) to get information about an address (recommended option). This is the recommended options because we have indexed all the data already for you.
2. You an also query a BitBadges blockchain node directly, either through the CLI or  [REST API ](https://docs.cosmos.network/v0.46/run-node/interact-node.html).&#x20;

# 👤 Accounts

This page will give you an overview of BitBadges accounts. It should be enough information for most, but for more low-level interaction, [the next page](accounts-technical.md) will give you more in-depth explanations.&#x20;

**What is signature compatibility?**

To enable interoperability between different blockchains, BitBadges is signature comaptible with all of the supported chains (Bitcoin, Ethereum, Solana, and Cosmos).&#x20;

Signature compatibility means that users from any of the aforementioned blockchain ecosystems are able to sign BitBadges transactions. BitBadges is also compatible with many of the wallets of each ecosystem. However, BitBadges is its own blockchain and does not pull any data from any other blockchain. Everything is confined to the BitBadges blockchain.

#### **How are accounts handled behind the scenes?**

To enable interoperability between different blockchains, each individual L1 blockchain will have its native addresses mapped to an equivalent Cosmos bech32 address and an account ID number. An account ID number will be assigned to the address when it interacts with the BitBadges blockchain or is sent $BADGE for the first time.&#x20;

As an example, the Ethereum null address 0x0000000000000000000000000000000000000000 maps to the Cosmos address cosmos1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a and will be assigned an account ID number upon interaction with the BitBadges blockchain. It would also map to an equivalent Bitcoin address as well.

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

#### **Representation** <a href="#addresses-and-public-keys" id="addresses-and-public-keys"></a>

Ethereum Example:

* Address (Bech32): `cosmos14au322k9munkmx5wrchz9q30juf5wjgz2cfqku`
* Address (Native - [EIP55](https://eips.ethereum.org/EIPS/eip-55) Ethereum Hex): `0xAF79152AC5dF276D9A8e1E2E22822f9713474902`

Solana Example:

* Address (Native - Base58): 6H2af68Yyg6j7N4XeQKmkZFocYQgv6yYoU3Xk491efa5
* Address (Bech32): cosmos18el5ug46umcws58m445ql5scgg2n3tzat53tsw

Bitcoin Example&#x20;

* Address (Native - P2WPKH): bc1q9s7rynm5pwhluhecsmlku8rn5yej5wdgj0gv3e
* Address (Bech32): cosmos19s7rynm5pwhluhecsmlku8rn5yej5wdgy4k845

#### Why can I convert Solana address to a  Cosmos / Eth / BTC address but not the other way around?

You may notice that you cannot go from a Cosmos / Eth address directly to a Solana address but you can the other way around. This is because conversion from a Solana address requires a hash, so if you just have the postimage of the hash (an Eth / Cosmos address), you cannot deduce the preimage without prior knowledge of it.

#### **Which address should I use (native or mapped Cosmos one)? How to convert?**

For user experience, you should always display the user's native address on a frontend. However, the BitBadges blockchain **only** uses the mapped Cosmos addresses behind the scenes, never a native address. This can be converted behind the scenes using the converter functions from [BitBadges SDK](../bitbadges-sdk/) (address-converter). This can be done with any validly formatted address.

<pre class="language-typescript"><code class="lang-typescript"><strong>import { ethToCosmos, cosmosToEth, convertToCosmosAddress, mustConvertToBtcAddress } from 'bitbadgesjs-utils';
</strong><strong>
</strong><strong>
</strong><strong>const cosmosAddress = convertToCosmosAddress(address);
</strong>const ethAddress = convertToEthAddress(cosmosAddress);
const cosmosAddressFromSolana = convertToCosmosAddress(solAddress);
const btcAddress = mustConvertToBtcAddress(address);
//Note there is no cosmosToSolana or ethToSolana due to how the addresses work
//See above
</code></pre>

#### **How do I query details for an address?**

1. You can use the [BitBadges API](../bitbadges-api/api.md) to get information about an address (recommended option). This is the recommended options because we have indexed all the data already for you.
2. You an also query a BitBadges blockchain node directly, either through the CLI or  [REST API ](https://docs.cosmos.network/v0.46/run-node/interact-node.html).&#x20;

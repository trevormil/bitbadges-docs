# 👤 Handling Addresses

This page will give you an overview of BitBadges accounts. It should be enough information for most, but for more low-level interaction, [this page](core-concepts/general/accounts-technical.md) will give you more in-depth explanations.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Mapping to a Common Address (bitbadgesAddress)**

To enable interoperability between different blockchains, each individual L1 blockchain will have its native addresses mapped to an equivalent Cosmos (aka BitBadges) bech32 address. We use the mapped **bitbadgesAddress** as the universal base address whenever needed.

We try to be as accommodating of native addresses as possible, but there are many places where universal standardization is needed. You will often come across places in development where you need to specify a **bitbadgesAddress.** This is expected to be the mapped address and not the native address (thus, you will need to map first). The typical naming convention we use is **bitbadgesAddress** vs **address.**&#x20;

The mappings should ONLY happen behind the scenes. On the user facing side, you should always display the user's native address.

Ethereum Example:

* Mapped BitBadges Address (Bech32): bb14au322k9munkmx5wrchz9q30juf5wjgz2cfqku
* Address (Native - [EIP55](https://eips.ethereum.org/EIPS/eip-55) Ethereum Hex): 0xAF79152AC5dF276D9A8e1E2E22822f9713474902

Solana Example:

* Address (Native - Base58): 6H2af68Yyg6j7N4XeQKmkZFocYQgv6yYoU3Xk491efa5
* Mapped BitBadges Address (Bech32): bb18el5ug46umcws58m445ql5scgg2n3tzagfecvl

Bitcoin Example

* Address (Native - P2WPKH): bc1q9s7rynm5pwhluhecsmlku8rn5yej5wdgj0gv3e
* Mapped BitBadges Address (Bech32): bb19s7rynm5pwhluhecsmlku8rn5yej5wdg8g75f9

#### Why can I convert Solana address to a BitBadges / ETH / BTC address but not the other way around?

You may notice that you cannot go from a BitBadges / ETH / BTC address directly to a Solana address but you can the other way around. This is because conversion from a Solana address requires a hash, so if you just have the postimage of the hash (an ETH / BitBadges address), you cannot deduce the preimage without prior knowledge of it.

#### **How to convert?**

The mapped addresses can be converted behind the scenes using the converter functions from [BitBadges SDK](bitbadges-sdk/) (address-converter). This can be done with any validly formatted address.

<pre class="language-typescript"><code class="lang-typescript"><strong>import { convertToEthAddress, convertToBitBadgesAddress, mustConvertToBtcAddress } from 'bitbadgesjs-sdk';
</strong>
<strong>const bitbadgesAddress = convertToBitBadgesAddress(btcAddress);
</strong>const ethAddress = convertToEthAddress(bitbadgesAddress);
const bitbadgesAddressFromSolana = convertToBitBadgesAddress(solAddress);
const btcAddress = mustConvertToBtcAddress(address);

// Note there is no convertToSolanaAddress due to how the addresses work. See above
</code></pre>

**What is signature compatibility?**

To enable interoperability between different blockchains, BitBadges is signature compatible with all of the supported chains (Bitcoin, Ethereum, Solana, and Cosmos).

Signature compatibility means that we can verify transaction signatures from any wallet of any supported ecosystem. BitBadges is its own blockchain and does not pull any data from any other blockchain. Everything is confined to the BitBadges blockchain.

#### **How do I query details for an address?**

1. You can use the [BitBadges API](bitbadges-api/api.md) to get information about an address (recommended option). This is the recommended options because we have indexed all the data already for you.
2. You an also query a BitBadges blockchain node directly, either through the CLI or [REST API](https://docs.cosmos.network/v0.46/run-node/interact-node.html)

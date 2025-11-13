# 👤 Multi-Chain Accounts

```
NOTE: This feature is planned to be deprecated soon in favor of only supporting wallets natively supported by the Cosmos SDK. The multi-chain account functionality will remain available on the backend for developers building custom applications, but support for this feature will be discontinued on the official BitBadges site.
```

### ⚠️ **Important Warning for Custom Applications**

If you are building custom applications that utilize multi-chain account functionality, **exercise extreme caution** when introducing logic that interacts with other chains for non-Cosmos based wallets. Other blockchains do **not** support the same address mapping scheme that BitBadges uses for non-Cosmos assets.

**Critical considerations:**

-   The address mapping scheme used by BitBadges is specific to the BitBadges blockchain
-   Other chains (Ethereum, Solana, Bitcoin, etc.) do not recognize or support these mappings
-   Attempting to use BitBadges address mappings on other chains can result in **stuck or lost user funds**
-   Always use native addresses when interacting with other blockchains directly
-   Only use the mapped BitBadges addresses within the BitBadges ecosystem

**Best practice:** Keep all address mapping logic isolated to BitBadges-specific operations. When bridging or interacting with external chains, always convert back to native addresses first.

### **How is BitBadges able to support addresses from different blockchains?**

To enable interoperability between different blockchains, BitBadges is signature compatible with all of its supported chains (Bitcoin, Ethereum, Solana, and Cosmos). In other words, we support any wallet type.

Signature compatibility means that users from any of the above blockchain ecosystems are able to sign BitBadges transactions, and we simply verify the signatures on our blockchain.

BitBadges is compatible with the wallets of each ecosystem. However, BitBadges is its own blockchain and does not pull any data from or is interoperable with any other blockchain. Everything is confined to the BitBadges blockchain.

<figure><img src="../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

### **Why do I see multiple equivalent addresses?**

All addresses map to an equivalent one in a different ecosystem (see the image below). You may be used to seeing your address as an Ethereum address, but behind the scenes, your mapped BitBadges address may be used for record keeping.

<figure><img src="../.gitbook/assets/image (88) (1).png" alt=""><figcaption></figcaption></figure>

### **Which chains / wallets are supported?**

Currently, we support Ethereum, Cosmos, Solana, Bitcoin.

**Mapping to a Common Address (bitbadgesAddress)**

To enable interoperability between different blockchains, each individual L1 blockchain will have its native addresses mapped to an equivalent Cosmos (aka BitBadges) bech32 address. We use the mapped **bitbadgesAddress** as the universal base address whenever needed.

We try to be as accommodating of native addresses as possible, but there are many places where universal standardization is needed. You will often come across places in development where you need to specify a **bitbadgesAddress.** This is expected to be the mapped address and not the native address (thus, you will need to map first). The typical naming convention we use is **bitbadgesAddress** vs **address.**

The mappings should ONLY happen behind the scenes. On the user facing side, you should always display the user's native address.

Ethereum Example:

-   Mapped BitBadges Address (Bech32): bb14au322k9munkmx5wrchz9q30juf5wjgz2cfqku
-   Address (Native - [EIP55](https://eips.ethereum.org/EIPS/eip-55) Ethereum Hex): 0xAF79152AC5dF276D9A8e1E2E22822f9713474902

Solana Example:

-   Address (Native - Base58): 6H2af68Yyg6j7N4XeQKmkZFocYQgv6yYoU3Xk491efa5
-   Mapped BitBadges Address (Bech32): bb18el5ug46umcws58m445ql5scgg2n3tzagfecvl

Bitcoin Example

-   Address (Native - P2WPKH): bc1q9s7rynm5pwhluhecsmlku8rn5yej5wdgj0gv3e
-   Mapped BitBadges Address (Bech32): bb19s7rynm5pwhluhecsmlku8rn5yej5wdg8g75f9

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

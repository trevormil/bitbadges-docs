# 💻 Multi-Chain Accounts

### **How is BitBadges able to support addresses from different blockchains?**

To enable interoperability between different blockchains, BitBadges is signature compatible with all of its supported chains (Bitcoin, Ethereum, Solana, and Cosmos).

Signature compatibility means that users from any of the above blockchain ecosystems are able to sign BitBadges transactions, and we simply verify the signatures on our blockchain.

BitBadges is compatible with the wallets of each ecosystem. However, BitBadges is its own blockchain and does not pull any data from or is interoperable with any other blockchain. Everything is confined to the BitBadges blockchain.

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

### **Why do I see multiple equivalent addresses?**

All addresses map to an equivalent one in a different ecosystem (see the image below). You may be used to seeing your address as an Ethereum address, but behind the scenes, your mapped BitBadges address may be used for record keeping.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **Which chains / wallets are supported?**

Currently, we support Ethereum, Cosmos, Solana, Bitcoin.

### Why is it per ecosystem and not per chain?

As you may have noticed, we do not differentiate between EVM chains, for example. All are treated the same with our architecture, so we just default to the emain chain of the ecosystem for display purposes (e.g. EVM for Ethereum).

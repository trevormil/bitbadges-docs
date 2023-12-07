# 🔏 Verifying Badge Ownership

There are many ways to verify if a user owns a badge or not. You can select your preferred method according to your use case and requirements.

Remember, **verification is two-fold**. First, you need to verify the user owns the address.  Second, you need to verify ownership of the badge according to your criteria.

We recommend using [Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) for verification because it supports native badge verification, is multi-chain compatible, and provides out-of-the-box support for important authentication details like nonces to avoid replay attacks.

**Step 1: Verify Balances**

Balances can be verified by querying them somehow, which can be via the BitBadges website, BitBadges API, directly from source for off-chain balances, or directly from a blockchain node. You can run your own API / node or self-host balances for further decentralization and other benefits like low latency, availability, etc. Keep in mind the potential delay / lag, availability, and pros and cons for all the options .

**Step 2: Verify Address Ownership**

Verifying address ownership can be done with a cryptographic signature. As mentioned above, we recommend Blockin; however, any cryptographic signature will do.

### Design Choices

Because the blockchain is public and decentralized, there are many different tools and options that you can use to customize your verification process, each offering various pros and cons. Check out [Ecosystem](../overview/ecosystem/) for all of them.





Below, we highlight some things you will need to consider:

**Pre-Generated Signatures**

Do you expect users to verify their address (i.e. sign a message) at authentication time? Do you expect them to have access to their crypto wallets at authentication time?

In a digital setting like gaining access to a website, you can probably assume that users can sign stuff with their crypto wallets at authentication time.

However, for in person authentication, you may want to consider pre-generating signatures, such as via a QR code or with NFC Readers, which can be signed beforehand and presented at authentication time instead of having users carry around their crypto wallets.

See [Generating Auth QR Codes](generating-auth-qr-codes.md) for a tutorial and more information.

**Offline vs Online**

Will your verification system have access to the Internet? If not, you need to obtain a snapshot of the badge balances via your preferred method (when you do have Internet) and use that in an offline setting. Blockin makes this easy.

Note for verification without badge balances, there is no Internet access requirement becuase cryptographic signatures can be verified offline.

**Decentralization**

How much do you care about decentralization? Are you okay to use centralized tools like the official BitBadges API, or do you want to run your own node / indexer?

**Latency, Availability, Throughput**

What are the requirements you need to support your users?



## Tools and Common Use Cases

**Multi-Chain Web3 Authentication w/ BitBadges Support - Blockin**

Check out [Blockin](https://blockin-quickstart.vercel.app) which is a universal, multi-chain sign-in standard for Web 3.0 which extends Sign-In with Ethereum for native BitBadges ownership verification. It is a TypeScript library which allows you to a) authenticate users (via a wallet cryptographic signature from any supported chain's wallet) and b) verify a user's ownership of assets (on multiple L1 chains including BitBadges)!

Blockin can be used for badge-gating websites and more!

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

# Overview

There are many ways to verify if a user owns a badge or not. You can select your preferred method according to your use case and requirements. Remember, **verification is two-fold**. First, you need to verify the user owns the address.  Second, you need to verify ownership of the badge according to your criteria.

We recommend using [Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) for verification. [Blockin](https://blockin-quickstart.vercel.app) is a universal, multi-chain sign-in standard for Web 3.0 which extends Sign-In with Ethereum for native BitBadges ownership verification. It is a TypeScript library which allows you to a) authenticate users (via a wallet cryptographic signature from any supported chain's wallet) and b) verify a user's ownership of assets (on multiple L1 chains including BitBadges)! Blockin can be used for badge-gating websites and more!

**Step 1: Query Balances**

Balances can be verified by querying them somehow, which can be via the BitBadges website, BitBadges API, directly from source for off-chain balances, or directly from a blockchain node. You can run your own API / node or self-host balances for further decentralization and other benefits like low latency, availability, etc. Keep in mind the potential delay / lag, availability, and pros and cons for all the options .

**Step 2: Verify Address Ownership**

Verifying address ownership can be done with a cryptographic signature. As mentioned above, we recommend Blockin; however, any cryptographic signature will do.

If you simply need to query balances and not verify address ownership, you do not need badge verification.

### Design Choices

Because the blockchain is public and decentralized, there are many different tools and options that you can use to customize your verification process, each offering various pros and cons. Check out [Ecosystem](../../overview/ecosystem/) for all of them.



<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

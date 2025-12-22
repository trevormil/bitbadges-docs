---
description: >-
  Here, you will find documentation about BitBadges, how it works, how to
  interact, and how to contribute!
---

# 👋 BitBadges Overview

## 🚀 The Next-Generation Token Standard

BitBadges has built a brand new tokenization standard exclusively as a Cosmos SDK module, designed specifically for RWAs (Real World Assets), compliance, payments, and custom transferability requirements.&#x20;

Unlike existing standards (`x/bank`, `x/tokenfactory`, `x/nft`, ICS20, ERC20, ERC-3643), our `x/tokenization` module provides native support for compliance checks on every transfer (even IBC transfers and in liquidity pools), custom transferability / compliance rules, issuer-level control, and enterprise-grade tokenization features—all out of the box with no code or smart contracts required, just a module! All plug-and-play and infinitely customizable.&#x20;

Our revolutionary token standard goes far beyond ERC-20, ERC-721, and other existing standards with features like time-dependent ownership, fine-grained transferability controls, multi-chain compatibility, connecting to 7000+ apps, connecting to EVM, IBC, CosmWASM, and more.

Our theses are:

1. The next wave of tokenization needs a next-generation standard. Existing ones are not enough and built on outdated technology.
2. Compliance / transferability is not just a matter of a simple whitelist/blacklist or transferable vs soulbound. It is a complex series of moving parts (time-gating, ownerships, approvals, who can send to who?, initiated by who?, revokable? freezable?, and so on). To truly make compliance work on-chain, you need to handle all these moving parts automatically, not with a manually updated whitelist/blacklist. And, this belongs on the token standard level.
3. The standardized, reusable, no-code approach wins over per use-case smart contracts over time.

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## ❓ Why BitBadges?

BitBadges is simply tokenization-as-a-service. Create anything from subscriptions and memberships to tradable NFTs, credentials, and access tokens - all with the most advanced token standard ever built.

Traditional token standards are limited, inflexible, and locked to single blockchain ecosystems. BitBadges fixes this with a 100x improvement that supports:

* **No Code, No Smart Contracts, No Audits** - Everything works out-of-the-box with no code. One reusable module.
* **Compliance Checked Every Transfer, Swap, IBC Transfer** - Build complex transferability systems checked everywhere. No backdoors. Compliance checked every swap.
* **Drop-In 1000x Upgrade -** We are a superset of existing standards. One line of code change for 1000x unlock in features.
* **Supports Any IBC (ICS20) Currency -** We've designed it in a way such that it is seamlessly compatible with any ICS20 currency paired for payments, swaps, liquidity, or anything else.
* **IBC Compatibility** - One interface, one token experience for all blockchain ecosystems via IBC.
* **Time-Dependent Ownership** - Create subscriptions, time-locked tokens, and expiring credentials with time-dependent logic and approvals.
* **Advanced Transferability / Compliance** - Fine-grained controls over who can transfer what, when, and how on any level.&#x20;
* **Three Transferability Levels** - Customize transferability on the collection, sender,and recipient levels.&#x20;
* **Connect to 7000+ Apps** - Connect to 7000+ apps and integrations with seamless on/off-chain criteria checks
* **Connect to Cosmos via IBC** - Connect to Cosmos and beyond via IBC and use the BitBadges token standard on any Cosmos chain
* **Extend with CosmWASM or EVM Contracts** - Extend the BitBadges token standard with CosmWASM or EVM contracts or any other custom environment
* **Customizable Permissions** - Flexible manager controls for collections

## 🤔 Motive for building BitBadges?

The answer is simple. We believe in the potential of blockchains and a multi-chain world, but this potential cannot be realized with the current infrastructure and token standards in place today.

## ⚠️ Problems with Existing Standards

Existing tokenization standards (ERC-20, ERC-721, CW-20, ICS-20, etc.) are **flawed from the ground up**:

* **Too Simple** - Basic mint/transfer/burn functionality lacks the flexibility needed for 90% of real-world applications. The industry has been stuck with these limited standards for 10+ years due to technical debt.
* **Vulnerable by Default** - Smart contract approach introduces new attack vectors with each token contract. Each deployment is a potential vulnerability.
* **Complex & Expensive** - Requires extensive technical knowledge to implement, deploy, and maintain contracts.
* **Low Interoperability** - Tokens are siloed to single ecosystems, forcing companies to split their userbase across chains.
* **Fragmented Standards** - Many competing standards with incompatible twists create confusion and fragmentation.

**The whole tokenization approach needs a complete overhaul.**

## 💡 Our Design Philosophy

BitBadges addresses these fundamental issues through core design decisions:

* **Universality** - One standard powerful enough for any use case: NFTs, fungible tokens, subscriptions, credentials, RWAs, compliance, or anything you can imagine. One standard to rule them all.
* **No-Code Module Approach** - Built as a Cosmos SDK module, not smart contracts. 99% of users will never need to write code, regardless of complexity. Promotes reusability and battle-tested security.
* **Ever-Evolving** - Purpose-built for next-generation tokenization. We're not stuck in the past like ERC-20/721. New features are added continuously with no technical debt accrual.
* **IBC-First** - Cosmos-native with IBC at the core. Custom wrappable to ICS-20/721, supports IBC denominations for payments/swaps/liquidity, and enables one-signature multi-hop IBC transfers.

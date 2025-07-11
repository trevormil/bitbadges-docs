---
description: >-
  Here, you will find documentation about BitBadges, how it works, how to
  interact, and how to contribute!
---

# 👋 BitBadges Overview

<figure><img src=".gitbook/assets/bitbadgeslogo.png" alt="" width="188"><figcaption></figcaption></figure>

## Overview

BitBadges offers tokenization-as-a-service and gating-as-a-service. We offer a multi-chain, state-of-the-art, community-driven token standard and suite of tools (Sign In with BitBadges, attestations, claim checking) for you to gate any app or service from websites to in-person events to Discord servers to repositories. Anything you want!

BitBadges can be simply thought of in two parts.

1. Criteria Checking: Let us do the heavy lifting of checking the criteria through multi-chain authentication, criteria checks from 7000+ apps, multi-chain badges / NFTs, points, address lists, and much more!
2. Utility - Seamlessly offer gated services like websites, URLs, tiered perks, subscriptions, anything to users who meet the criteria!

By checking any criteria and delivering any reward, you can gate anything!

<figure><img src=".gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The BitBadges ecosystem aims to offer the full-stack of tools and services you may need from the required storage (blockchain, data indexing, off-chain data storage) to a universal multi-chain authentication standard to distribution to offline-first verification tools (in-person verification, website gated sign-ins) to communicating with your badge holders and more!

### **Motive for building BitBadges?**

The answer is simple. We believe in the potential of blockchains and a multi-chain world, but this potential cannot be realized with the current infrastructure and technology in place today. Current infrastructure is not scalable, lacks consistency, limited in features, and is limited to a single blockchain ecosystem at a time. BitBadges wants to help by building the critical infrastructure the right way!

This applies to not only our token standard but everything we do. We have built everything from the ground up to support multiple blockchain ecosystems all with the SAME interface. All while maintaining our core values of decentralization.

No longer do you need to support multiple interfaces for each blockchain ecosystem you want to support. For example, you can authenticate users from Cosmos, Solana, Bitcoin, and Ethereum all with the same interface. All users can own the SAME badges, use the SAME protocols, and so on. Before BitBadges, building multi-chain applications was a nightmare due to having to support all the various interfaces, protocols, and more for each blockchain ecosystem. Now, it is all in one place.

<div data-full-width="false"><figure><img src=".gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure></div>

## Our Services

### Badges

With our innovative 100x token standard, create multi-chain tokens (badges) that can be owned, transferred, and traded by BitBadges accounts. By multi-chain, users from ANY chain can own and transfer the SAME token (badge)! Something completely novel in the Web3 ecosystem today.

Tokens (badges) are simply something that you can own digitally and prove ownership of it. You probably already use and own many digital tokens: verification checkmarks, a movie streaming subscription, concert tickets, etc. These tokens can be used for many [use cases](./#use-cases), each potentially offering you different utility and value. Some may have real-world use cases (e.g. entry to a concert), while some may be purely digital (e.g. verification checkmark). Some may signify something about your reputation (certifications), while some may just be collected for fun.

When combined with blockchain technology, badges become even more secure and more powerful, due to the unique properties of the blockchain (verifiable, decentralized, tamper-proof, and so on). However, the existing infrastructure and technology is not nearly good enough to realize the vast potential of digital blockchain tokens (not scalable, lacks consistency, and is limited to a single blockchain ecosystem at a time).

### Alternatives - Lists, Protocols, Attestations, Points, Tiers, Quests

We also offer alternatives to badges to enhance your digital identity and portfolio:

1. **Address Lists:** Manage users from any supported blockchain with simple address lists. This streamlined alternative to traditional badges allows for efficient user management across multiple chains.
2. **Protocols / Maps:** Leverage reusable protocols to manage information such as preferences, reputation, and more across applications and chains. These protocols create a standardized way to store and access data, enhancing interoperability between different platforms and ecosystems.
3. **Attestations:** Use attestations to prove information, such as credentials, privately with zero-knowledge selective disclosure. This feature allows you to verify specific details without revealing unnecessary personal information, enhancing privacy and security in digital interactions.
4. **Points / Tiers / Quests** - Gamify user experiences with a points based leaderboard, quests, or gamified tiers they can earn.

These additional tools complement our badge system, providing a comprehensive suite for managing digital identities and interactions in the blockchain space.

### Universal Authentication - Sign In with BitBadges

Putting it all together, think of your BitBadges portfolio as a digital data backpack / identity. You own it. You control it. You take it with you everywhere you go and can use it to prove anything.

![](image-1.png)

Thus, BitBadges becomes the all-in-one authentication provider, revolutionizing how users prove their identity and credentials across various platforms.  Use Sign In with BitBadges to authenticate users and check any criteria (claims, badge ownership, attestations, etc) all in one flow!

### BitBadges Claims

Claims are a core aspect of BitBadges allowing you to check criteria from over 7000+ apps and integrations. Tons of plugins are supported directly no-code and in-site, but we also have flexible implementation options to allow you to add your own logic (webhooks, API, building / publishing your own plugin).

The most powerful feature of claims is that they are compatible with every other aspect of BitBadges (ex: gating badge distribution with claims, Sign In with BitBadges and check claims all in one flow).

Combine criteria checks to gate anything from content to websites to badge distribution to anything you want! Let BitBadges do the heavy lifting, enabling you to focus on offering your core utility.

<figure><img src=".gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

### Zapier

BitBadges has a Zapier integration that allows you to connect BitBadges to 7000+ apps supported by Zapier ([https://zapier.com/apps](https://zapier.com/apps)).

![](image.png)

## Use Cases

{% content-ref url="overview/use-cases.md" %}
[use-cases.md](overview/use-cases.md)
{% endcontent-ref %}

## 100x Token Standard

The BitBadges token (badge) standard is state-of-the-art compared to existing token standards (ERC20, ERC721, etc) and does not require smart contracts. The standard is ever-evolving and natively supports never-before-seen features like time-dependent ownership, fine-grained transferability requirements, and hybrid off-chain balances. We like to think of BitBadges as a 100x improvement over existing token standards.

In addition to the standard features of existing token standards (mint, transfer, approve, etc), we expand and offer the following functionality:

* **Time-Dependent Balances:** Badge balances are all time-dependent which allow you to, for example, transfer only a specific period of time (e.g. subscription token for a month), clearly define token unlock schedules, or approve a transfer only for a specific period of time.
* **Off-Chain Balances:** New ways to store and track balances, in addition to the standard on-chain storage of balances. Storing balances off-chain can offer over 1000x better scalability and much better user experience because users never need to transact with the blockchain. Badges are auto assigned to their wallets. This also allows seamless connection to any Web2 app since you are not limited to only blockchain data anymore.
* **Fine-Grained Transferability and Approvals Customization:** Simply abstracting transferability to "transferable" or "non-transferable" is too simple for many use cases. We recognize that transferability is a complex protocol of who can transfer to who? at what times? what badges? how many? revokable? freezable? etc.
  * Example: Only those who own the verified checkmark badge can transfer the badge IDs 1-5 to each other from Monday to Tuesday 12PM, but badges will be revokable by the manager after that.
  * **Fine-Grained Approvals:** In addition to simply specifying approval of X amount, you can customize approvals further with details like predetermined balances (x1 of ID 1, then x1 of ID 2), max number of transfers allowed, and more!
  * **Incoming Approvals:** In addition to having control over your outgoing transfers, have control over your incoming transfers via incoming approvals.
    * Ex: Block certain users from transferring to you. Block all transfers unless you opt-in to receiving them.
  * And more!
* **Customizable Permissions:** Each collection has fine-grained customizable permissions that can be optionally set and executed by a special party called the manager, such as archiving the collection, deleting it, updating its metadata, updating transferability, etc.
* **Time-Based Details:** Important collection details such as metadata are time-based, allowing you to automatically commit to updating it at a future time without needing to transact at that time. Ex: Set the metadata to be one value from January 1 to January 10 and then auto-change to another value!
* **Batch Transfers:** Batch transfer badges instead of only being able to transfer one by one.
  * Instead of needing 1000 transactions to send 1000 unique non-fungible badges in a collection (e.g. x1 of Badge ID 1, x1 of ID 2, ...), you can batch all into one transaction efficiently (e.g. send x1 of Badge IDs 1-1000).

And much more!

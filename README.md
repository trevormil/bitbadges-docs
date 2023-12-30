---
description: >-
  Here, you will find documentation about BitBadges, how it works, how to
  interact, and how to contribute!
---

# 👋 BitBadges Overview

<figure><img src=".gitbook/assets/bitbadgeslogo.png" alt="" width="188"><figcaption></figcaption></figure>

## Overview&#x20;

Tokens (badges) are simply something that you can own digitally and prove ownership of it. You probably already use and own many digital tokens: verification checkmarks, a movie streaming subscription, concert tickets, etc. These tokens can be used for many [use cases](./#use-cases), each potentially offering you different utility and value. Some may have real-world use cases (e.g. entry to a concert), while some may be purely digital (e.g. verification checkmark). Some may signify something about your reputation (certifications), while some may just be collected for fun.

When combined with blockchain technology, badges become even more secure and more powerful, due to the unique properties of the blockchain (verifiable, decentralized, tamper-proof, and so on).&#x20;

However, the existing infrastructure and technology is not nearly good enough to realize the vast potential of digital blockchain tokens. Notably, existing state-of-the-art is not scalable, lacks consistency, and is limited to a single blockchain ecosystem at a time (e.g. only Ethereum users). This is a HUGE problem because most companies and providers are neutral and do not belong to a specific blockchain ecosystem, so currently, it greatly restricts their potential audience! Imagine hosting a concert that can only be attended by those with Android phones.&#x20;

**BitBadges can be described as tokenization-as-a-service. We offer an open-source, multi-chain, state-of-the-art, community-driven token standard and suite of tools that enable you to create, customize, verify, and integrate with digital blockchain tokens for any use case you desire.**

The BitBadges ecosystem aims to offer the full-stack of tools and services you may need from the required storage (blockchain, data indexing, off-chain data storage) to authentication to distribution to offline-first verification tools (in-person verification, website gated sign-ins) to communicating with your badge holders and more!

The web app ([https://bitbadges.io](https://bitbadges.io)) allows you to show off your badges and build your digital identity as you collect more over time! On the flip side, you can browse others' badges to get a sense of their digital identity (e.g. are they a scammer or trustworthy?). This app has a social media feel but is also the all-in-one site to establish the identity of a user.

<figure><img src=".gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Use Cases

{% content-ref url="overview/use-cases.md" %}
[use-cases.md](overview/use-cases.md)
{% endcontent-ref %}

Because you can create a badge for anything, there are infinitely many possible use cases for BitBadges! As you collect more badges, your portfolio grows, and you can show it off to others.

Below are some of our favorite use cases:

* **Attendance Badges -** Mint an attendance badge for an event, trip, etc as a souvenir!
* **Memberships/Subscriptions/Premium Content -** Badges are all time-based, so you can offer time-dependent memberships / subscriptions and offer the utility only to those who own the badge at a certain time!
* **Access Tokens** - Use badges as access tokens. This can be digital (websites, Discord servers, etc) or in-person (tickets, event entry, etc).
* **Recognition of Achievement or Completion** - Job certifications, awards, athletic accomplishments, completing a class, etc.&#x20;
* **Authentication / Tiered Services -** Companies can outsource their authentication to Web3 / [Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) and verify ownership through tiered services badges (family plan badge vs normal user badge). Cheaper, more secure, and less work!
* **Protocols:** Use BitBadges to implement multi-chain protocols, such as an attendance protocol or a follow protocol.
* See more use cases [here](broken-reference)!

## Why are we better?

What makes our token standard and product better than competitors?&#x20;

<div data-full-width="false">

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

</div>

#### **Multi-Chain**

Supports users from multiple blockchain ecosystems instead of just one! We currently support Cosmos, Solana, Bitcoin, and Ethereum users.

**Decentralization**

We keep decentralization as a core principle, as opposed to some of our competitors who rely on more centralized architectures and censoring. We ALWAYS provide fully decentralized options but also hybrid ones with different tradeoffs such as enhanced user experience for those who do not care about decentralization.

#### **Rapidly-Evolving**

**I**nstead of relying on a rigid token standard that is not adaptable to new features, we iterate fast and constantly add new features. We believe token standards should be flexible and continuously adding new functionality, as opposed to existing ones which are rigid, not adaptable, and hard to adopt new ones.

#### **Community-Driven Ecosystem**

By being open-source and developer friendly, we aim to facilitate an ecosystem of community-driven projects, tools, and more built on top of BitBadges. See [Ecosystem](overview/ecosystem.md).

#### **Scalability, Security, and Ease of Use**

Our product is more scalable, easier to use, and more secure than competitors (see [here](overview/faq.md#what-makes-bitbadges-better-than-competitors)) .

### Innovative Features

<figure><img src=".gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

In addition to the standard features of existing token standards (mint, transfer, approve, etc), we expand and offer the following functionality:

* [**Time-Based Balances**](overview/how-it-works/time-dependent-ownership.md)**:** Badge balances are all time-dependent which allow you to, for example, transfer only a specific period of time (e.g. subscription token for a month), clearly define token unlock schedules, or approve a transfer only for a specific period of time.&#x20;
* [**Off-Chain Balances:**](overview/how-it-works/balances-types.md) **N**ew ways to store and track balances, in addition to the standard on-chain storage of balances. Storing balances off-chain can offer over 1000x better scalability and much better user experience because users never need to transact with the blockchain. Badges are auto assigned to their wallets.
* [**Fine-Grained Transferability and Approvals Customization**](overview/how-it-works/transferability.md)**:**  Simply abstracting transferability to "transferable" or "non-transferable" is too simple for many use cases. We recognize that transferability is a complex protocol of who can transfer to who? at what times? what badges? how many? revokable? freezable? etc.&#x20;
  * Example: Only those who own the verified checkmark badge can transfer the badge IDs 1-5 to each other from Monday to Tuesday 12PM, but badges will be revokable by the manager after that.
  * [**Must Own Badges:** ](overview/how-it-works/transferability.md)Restrict sending and receiving badges to only those who own specific badges of other collections (e.g. a KYC badge, a verified badge).
  * [**Fine-Grained Approvals:** ](overview/how-it-works/transferability.md)In addition to simply specifying approval of X amount, you can customize approvals further with details like predetermined balances (x1 of ID 1, then x1 of ID 2), max number of transfers allowed, and more!
  * [**Incoming Approvals:** ](overview/how-it-works/transferability.md) In addition to having control over your outgoing transfers, have control over your incoming transfers via incoming approvals.
    * Ex: Block certain users from transferring to you. Block all transfers unless you opt-in to receiving them.
  * And more!
* [**Customizable Permissions**](overview/how-it-works/manager.md)**:**  Each collection has fine-grained customizable permissions that can be optionally set and executed by a special party called the manager, such as archiving the collection, deleting it, updating its metadata, updating transferability, etc.
* [**Time-Based Details**](for-developers/collection-interface/timelines.md)**:** Important collection details such as metadata are time-based, allowing you to automatically commit to updating it at a future time without needing to transact at that time.
  * Ex: Set the metadata to be one value from January 1 to January 10 and then auto-change to another value!
* [**Batch Transfers**](overview/how-it-works/time-dependent-ownership.md)**:** Batch transfer badges instead of only being able to transfer one by one.
  * Instead of needing 1000 transactions to send 1000 unique non-fungible badges in a collection (e.g. x1 of Badge ID 1, x1 of ID 2, ...), you can batch all into one transaction efficiently (e.g. send x1 of Badge IDs 1-1000).

And much more!

## Distribution Tools

There are infinitely many ways to distribute badges to holders. A few are supported natively by the BitBadges website (whitelists, passwords, claim codes, QR codes).  However, there is no way that we can natively support all distribution methods. Even if we could, this would centralize the badge distribution process.

BitBadges wants to decentralize the distribution process by supporting a vast ecosystem of community-built distribution tools. Our goal is that users will have thousands of options to choose from built by different teams, each offering their own unique niche.

See [Ecosystem](overview/ecosystem.md) for all the distribution tools we and the community offer. See [Creating a Distribution Tool](for-developers/tutorials/build-a-distribution-tool.md) for how to create one.

**Currently Supported**

We natively have support the following:

* Whitelists - Specify a list of users that can claim the badge (on-chain or off-chain).
* Password - Enter the correct password can claim a badge.
* Codes - Enter unique custom codes to claim a badge. Codes can be distributed however you would like (email, social media DMs, anything!).
* QR Codes - Restrict badges to be claimed by only those who scan a specific QR code.

## Ecosystem Tools

In addition to distribution tools, we aim to have thousands of community-built ecosystem tools for anything from authenticating users, communicating with badge holders, distributing badges, and more!

Similar to distribution tools, we want to support a vast ecosystem of tools built by different teams, each with their own unique niche. See [Ecosystem](overview/ecosystem.md) for some tools currently built.&#x20;

**Blockin - Badge-Gate Anything**

{% content-ref url="https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

Blockin is a universal, multi-chain sign-in interface that includes support for badge-gating websites. It extends Sign-In with Ethereum to support any and all blockchains.

With Blockin, you can restrict access to anything to only your badge holders! It can be used digitally through its developer libraries. Or, verify badge ownership in person with QR codes (supported on the web app).

<figure><img src=".gitbook/assets/image (22).png" alt="" width="467"><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (21).png" alt="" width="375"><figcaption></figcaption></figure>

## Links

BitBadges App - [https://bitbadges.io](https://bitbadges.io)

**Socials**

Discord - [https://discord.com/invite/TJMaEd9bar](https://discord.com/invite/TJMaEd9bar)

LinkedIn - [https://linkedin.com/company/bitbadges](https://linkedin.com/company/bitbadges)

Twitter - [https://twitter.com/bitbadges\_io](https://twitter.com/bitbadges\_io)

Facebook - [https://facebook.com/profile.php?id=100092259215026](https://facebook.com/profile.php?id=100092259215026)

Instagram - [https://instagram.com/bitbadges\_official/](https://instagram.com/bitbadges\_official/)

Slack - [https://bitbadges.slack.com/join/shared\_invite/zt-1tws89arl-TMSK\_4bdTLOLdyp177811Q#/shared-invite/email](https://bitbadges.slack.com/join/shared\_invite/zt-1tws89arl-TMSK\_4bdTLOLdyp177811Q#/shared-invite/email)

Reddit -[ ](https://www.reddit.com/r/BitBadges/)[https://www.reddit.com/r/BitBadges/](https://www.reddit.com/r/BitBadges/)

Telegram - [https://t.me/BitBadges](https://t.me/BitBadges)

GitHub - [https://github.com/bitbadges](https://github.com/bitbadges)

**Other**

Project Board -  [https://github.com/bitbadges/projects](https://github.com/orgs/BitBadges/projects)

Improvement Proposals - [https://github.com/BitBadges/BBIPs](https://github.com/BitBadges/BBIPs)&#x20;

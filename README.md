---
description: >-
  Here, you will find documentation about BitBadges, how it works, how to
  interact, and how to contribute!
---

# 👋 BitBadges Overview

<figure><img src=".gitbook/assets/bitbadgeslogo.png" alt="" width="188"><figcaption></figcaption></figure>

## Overview&#x20;

Digital tokens (badges) are simply something that you can own digitally and prove ownership of it. You probably already use and own many digital tokens: verification checkmarks, a movie streaming subscription, concert tickets, etc. These tokens can be used for infinitely many [use cases](./#use-cases), each potentially offering you different utility and value. Some may have real-world use cases (e.g. entry to a concert), and some may be purely digital (e.g. verification checkmark).&#x20;

When combined with blockchain technology, they become even more secure and more powerful, due to the unique properties of the blockchain. However, the existing infrastructure and technology is not nearly good enough to realize the vast potential of digital blockchain tokens. Notably, existing state-of-the-art is not scalable, lacks consistency, and is limited to a single blockchain ecosystem at a time (e.g. only Ethereum users or only Cosmos users).

**BitBadges can be described as tokenization-as-a-service. We offer an open-source, multi-chain, state-of-the-art, community-driven suite of tools that enable you to create, customize, verify, and integrate with digital blockchain tokens for any use case.**&#x20;

The BitBadges ecosystem aims to offer the full-stack of tools and services you may need from the required storage (blockchain, data indexing, off-chain data storage) to authentication to distributing badges to offline-first verification tools (in-person verification, website gated sign-ins) to communicating with your badge holders and more!

The BitBadges app ([https://bitbadges.io](https://bitbadges.io)) allows you to show off your badges and build your digital identity as you collect more over time! On the flip side, you can browse others' badges to get a sense of their digital identity (e.g. are they a scammer or trustworthy?). This app has a social media feel but is also the all-in-one site to learn about the identity of a user.

To learn more about BitBadges (the company), visit [https://bitbadges.org](https://bitbadges.org).

## Use Cases

{% content-ref url="overview/use-cases.md" %}
[use-cases.md](overview/use-cases.md)
{% endcontent-ref %}

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Because you can create a badge for anything, there are infinitely many possible use cases for BitBadges! As you collect more badges, your portfolio grows, and you can show it off to others.

Below are some of our favorite use cases:

* **Attendance Badges -** Mint an attendance badge for an event, trip, etc as a souvenir!
* **Memberships/Subscriptions/Premium Content -** Badges are all time-based, so you can offer time-dependent memberships / subscriptions and offer the utility only to those who own the badge at a certain time!
* **Access Tokens** - Use badges as access tokens. This can be digital (websites, Discord servers, etc) or in-person (tickets, event entry, etc).
* **Recognition of Achievement or Completion** - Job certifications, awards, athletic accomplishments, completing a class, etc.&#x20;
* **Authentication / Tiered Services -** Most companies' infrastructure simply consists of authentication + tiered services. Companies can outsource their authentication to Web3 / [Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) and verify ownership through tiered services badges (family plan badge vs normal user badge). Much cheaper, more secure, and less work!
* See more [here](broken-reference)!

## Why are we better?

What makes our product better than competitors?

**Multi-Chain:** Supports users from multiple blockchain ecosystems instead of just one!

<figure><img src=".gitbook/assets/image (3).png" alt="" width="179"><figcaption></figcaption></figure>

* Imagine if a sporting event was limited to only Android users because the ticketing technology only worked on Android! This is the current state of infrastructure.
* Currently, token standards are limited to one blockchain ecosystem (e.g. only Ethereum users or only Solana users). This is a HUGE problem because most companies do not belong to a specific blockchain ecosystem, so it greatly restricts their potential userbase!



**Decentralization:** We keep decentralization as a core principle, as opposed to some of our competitors who rely on more centralized architectures and censoring.

**Rapidly-Evolving:** Instead of relying on a rigid token standard that is not adaptable to new features, we iterate fast and constantly add new features to our token standard.

**Community-Driven:** By being open-source and developer friendly, we aim to facilitate an ecosystem of community-driven projects, tools, and more built on top of BitBadges. See [Ecosystem](overview/ecosystem.md).

**Scalability, Security, and Ease of Use:**  Our product is more scalable, easy to use, and battle-tested (see [here](overview/faq.md#what-makes-bitbadges-better-than-competitors)) than competitors.

* This is because instead of having to write new custom code for every collection of tokens (as existing standards do which often results in security vulnerabilities), we use a registry architecture where code is reused by all collections.&#x20;

#### Newly Innovated Features

In addition to the standard features of existing token standards (mint, transfer, approve, etc), we offer the following newly innovated features:

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

* [**Time-Based Balances**](overview/concepts/time-dependent-ownership.md)**:** Badge balances are all time-based which allow you to, for example, transfer only a specific period of time (e.g. subscription token for a month), clearly define token unlock schedules, or approve a transfer only for a specific period of time.&#x20;
* [**Off-Chain and Inherited Balances:**](overview/concepts/balances-types.md) Two new ways to store and track balances, in addition to the standard on-chain storage of balances. These can offer over 1000x better scalability and much better user experience.
  * 1\) Off-chain balances allow you to host your balances via a typical server, reducing the resources used by the blockchain for maintaining balances down to just the size of a URL! This is as opposed to kilobytes and megabytes for standard balances.
    * Users never need to transact. Badges are auto assigned to their wallets.
    * No on-chain transfers or approvals are allowed so only usable by select use cases.
  * 2\) Inherited balances allow the owners of one badge to automatically be bounded to another (parent-child relationship).
* [**Batch Transfers**](overview/concepts/time-dependent-ownership.md)**:** Batch transfer badges instead of only being able to transfer one by one.
  * Instead of needing 1000 transactions to send 1000 unique non-fungible badges (e.g. x1 of Badge ID 1, x1 of ID 2, ...), you can batch all into one transaction efficiently (e.g. send x1 of Badge IDs 1-1000).
* [**Fine-Grained Transferability Customization**](overview/concepts/transferability.md)**:**  Simply abstracting transferability to "transferable" or "non-transferable" is too simple for many use cases. We recognize that transferability is a complex protocol of who can transfer to who? at what times? what badges? how many? revokable? freezable? etc.
  * Example: Only those who own the verified checkmark badge can transfer the badge IDs 1-5 to each other from Monday to Tuesday 12PM, but badges will be revokable by the manager after that.
  * [**Must Own Badges:** ](overview/concepts/transferability.md)Restrict sending and receiving badges to only those who own specific badges of other collections (e.g. a KYC badge, a verified badge).
  * [**Fine-Grained Approvals:** ](overview/concepts/transferability.md)In addition to simply specifying approval of X amount, you can customize approvals further with details like predetermined balances (x1 of ID 1, then x1 of ID 2), max number of transfers allowed, and more!
  * [**Incoming Approvals:** ](overview/concepts/transferability.md) In addition to having control over your outgoing transfers, have control over your incoming transfers via incoming approvals.
    * Ex: Block certain users from transferring to you. Block all transfers unless you opt-in to receiving them.
  * And more!
* [**Customizable Permissions**](overview/concepts/manager.md)**:**  Enable customizable permissions for your collection, such as archiving the collection, deleting it, updating its metadata, updating transferability, etc.
  * Includes fine-grained controls such as when each permission can be executed? for what values? for what times?
* [**Time-Based Details**](for-developers/collection-interface/timelines.md)**:** Important collection details such as metadata are time-based, allowing you to automatically commit to updating it at a future time without needing to transact at that time.
  * Ex: Set the metadata to be one value from January 1 to January 10 and then auto-change to another value!

And much more!

## Distribution Tools

There are infinitely many ways to distribute badges to holders. A few are supported natively by the BitBadges website (whitelists, passwords, claim codes, QR codes).&#x20;

However, there is no way that we can natively support all distribution methods. Even if we could, this would centralize the badge distribution process.

BitBadges wants to decentralize the distribution process by supporting a vast ecosystem of community-built distribution tools. Our goal is that users will have thousands of options to choose from built by different teams, each offering their own unique niche.

See [Ecosystem](overview/ecosystem.md) for all the distribution tools we and the community offer.

**Currently Supported**

We currently support the following:

* Whitelists - Specify a list of users that can claim the badge.
* Password - Enter the correct password can claim a badge.
* Codes - Enter unique custom codes to claim a badge. Codes can be distributed however you would like (email, social media DMs, anything!).
* QR Codes - Restrict badges to be claimed by only those who scan a specific QR code.

**Future Ideas**

Here are some future ideas that could be implemented

* Claim by Geolocation
* Raffles&#x20;
* In-Person QR Code kiosks
* Restrict to your social media followers
* And much more

See [Creating a Distribution Tool](for-developers/tutorials/build-a-distribution-tool.md) for how to create one.

## Authentication Tools

Authentication tools enable you to verify users are who they say they are and/or verify they own specific badges!

**Blockin - Badge-Gate Websites**

{% content-ref url="http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

With Blockin, you can restrict access to any website to only your badge holders!

Blockin is a universal, multi-chain sign-in interface that includes support for badge-gating websites. It extends Sign-In with Ethereum to support any and all blockchains.

**Other Authentication Tools**

See [Ecosystem](overview/ecosystem.md) for all the authentication tools we offer.&#x20;

In the near future, we plan to allow you to authenticate via the following methods:

* QR Codes
* Discord Bots
* Barcode Scanners
* Zero-Knowledge Proof of Ownerships
* Snapshots
* And more!

## Developer Stack

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

## Links

BitBadges App - [https://bitbadges.io](https://bitbadges.io)

**Socials**

Discord - [https://discord.com/invite/TJMaEd9bar](https://discord.com/invite/TJMaEd9bar)

LinkedIn - [https://linkedin.com/company/bitbadges](https://linkedin.com/company/bitbadges)

Twitter - [https://twitter.com/bitbadges\_io](https://twitter.com/bitbadges\_io)

Facebook - [https://facebook.com/profile.php?id=100092259215026](https://facebook.com/profile.php?id=100092259215026)

Instagram - [https://instagram.com/bitbadges\_official/](https://instagram.com/bitbadges\_official/)

Slack - [https://bitbadges.slack.com/join/shared\_invite/zt-1tws89arl-TMSK\_4bdTLOLdyp177811Q#/shared-invite/email](https://bitbadges.slack.com/join/shared\_invite/zt-1tws89arl-TMSK\_4bdTLOLdyp177811Q#/shared-invite/email)

Crunchbase - [https://www.crunchbase.com/organization/bitbadges](https://www.crunchbase.com/organization/bitbadges)

Reddit -[ ](https://www.reddit.com/r/BitBadges/)[https://www.reddit.com/r/BitBadges/](https://www.reddit.com/r/BitBadges/)

Telegram - [https://t.me/BitBadges](https://t.me/BitBadges)

GitHub - [https://github.com/bitbadges](https://github.com/bitbadges)

BriefLink - [https://brieflink.com/v/ro6w4](https://brieflink.com/v/ro6w4)

**Other**

Project Board -  [https://github.com/bitbadges/projects](https://github.com/orgs/BitBadges/projects)

Improvement Proposals - [https://github.com/BitBadges/BBIPs](https://github.com/BitBadges/BBIPs)&#x20;

---
description: >-
  Here, you will find documentation about BitBadges, how it works, how to
  interact, and how to contribute!
---

# 👋 BitBadges Overview

<figure><img src=".gitbook/assets/bitbadges_letters.png" alt=""><figcaption></figcaption></figure>

## Overview&#x20;

Digital tokens (badges) are simply something that you can own digitally and prove ownership of it. You probably already use and own many digital tokens: verification checkmarks, a movie streaming subscription, concert tickets, etc. These tokens can be used for infinitely many [use cases](./#use-cases), each potentially offering you different utility and value. Some may have real-world use cases (e.g. entry to a concert), and some may be purely digital (e.g. verification checkmark).&#x20;

When combined with blockchain technology, they become even more secure and more powerful, due to the unique properties of the blockchain ([read more here](https://101blockchains.com/advantages-of-nfts/)). However, the existing infrastructure and technology is not nearly good enough to realize the vast potential of digital blockchain tokens ([see here](./#improvements-over-existing-products)).

**BitBadges can be described as tokenization-as-a-service. We offer an open-source, state-of-the-art, community-driven suite of tools that enable you to create, customize, verify, and integrate with digital blockchain tokens for any use case that you desire.**&#x20;

We offer the full-stack of tools and services you may need from the required storage (blockchain, data indexing, off-chain data storage) to authentication to distributing badges to offline-first verification tools (in-person verification, website gated sign-ins) to communicating with your badge holders and more!

Additionally, the BitBadges app ([https://bitbadges.io](https://bitbadges.io)) allows you to show off your badges and build your digital identity as you collect more over time! On the flip side, you can browse others' badges to get a sense of their digital identity (e.g. are they a scammer or trustworthy?). This app has a social media feel but is also the all-in-one site to learn about the identity of a user.

To learn more about BitBadges (the company), visit [https://bitbadges.org](https://bitbadges.org).

## Use Cases

{% content-ref url="overview/use-cases.md" %}
[use-cases.md](overview/use-cases.md)
{% endcontent-ref %}

* Attendance Badges - Create an attendance badge for an event, trip, etc.
* Memberships/Subscriptions/Access Tokens - Gym memberships, website subscriptions, etc
* Premium Content - Offer premium features and restrict access to only badge owners
* Recognition of Achievement or Completion - Show off your job certifications, awards, athletic accomplishments, etc.
* E-Learning - Gamify the learning experience through earning learning badges
* University Diplomas - Universities can offer verifiable diplomas to students as a badge.
* See more [here](broken-reference)!

## Why are we better?

What makes our product better than competitors?

* **Cross-Chain:** Supports users from multiple blockchain ecosystems instead of just one!
  * Currently, token standards are limited to one blockchain ecosystem (e.g. only Ethereum users or only Solana users). This is a HUGE problem because most companies do not belong to a specific blockchain ecosystem, so it greatly restricts their potential userbase!
  * Imagine if a sporting event was limited to only Android users because the ticketing technology only worked on Android!
* **Decentralization:** We keep decentralization as a core principle, as opposed to some of our competitors who rely on more centralized architectures.
* **Rapidly-Evolving:** Instead of relying on a rigid token standard that is not adaptable to new features, we iterate fast and constantly add new features to our token standard.
* **Community-Driven:** By being open-source and developer friendly, we aim to facilitate an ecosystem of community-driven projects, tools, and more built on top of BitBadges
* **Scalability, Security, and Ease of Use:**  Our product is more scalable, easy to use, and battle-tested (see [here](overview/faq.md#what-makes-bitbadges-better-than-competitors)) than competitors.
  * This is because instead of having to write new custom code for every collection of tokens (as existing standards do which often results in security vulnerabilities), we use a registry architecture where code is reused by all collections.&#x20;

#### Newly Innovated Features

* [**Time-Based Balances**](overview/how-it-works/balances-transfers.md)**:** Enables you to transfer a badge for only a specific period of time (e.g. subscription token for a month), clearly define token unlock schedules, or approve a transfer only for a specific period of time.&#x20;
* [**Off-Chain and Inherited Balances:**](overview/how-it-works/balances-types.md) Two new ways to store and track balances, in addition to the standard on-chain storage of balances. These can offer over 1000x better scalability and much better user experience.
  * 1\) Off-chain balances allow you to host your balances via a typical server, reducing the resources used by the blockchain by >99%.&#x20;
  * 2\) Inherited balances allow the owners of one badge to automatically be bounded to another.
* [**Batch Transfers**](overview/how-it-works/balances-transfers.md)**:** Batch transfer badges instead of only being able to transfer one by one.
  * Instead of needing 1000 transactions to send 1000 unique non-fungible badges (e.g. x1 of Badge ID 1, x1 of ID 2, ...), you can batch all into one transaction efficiently (e.g. send x1 of Badge IDs 1-1000).
* [**Fine-Grained Transferability Customization**](overview/how-it-works/transferability.md)**:**  Simply abstracting transferability to "transferable" or "non-transferable" is too simple for many use cases, such as those that require revoking badges, freezing transfers, etc.
  * We recognize that transferability is a complex protocol of who can transfer to who? at what times? what badges? how many? revokable? freezable? forceful transfers? or requires approval? what kind of approval? must own certain badges to be approved? And more!
  * Ex: Only those who own the verified checkmark badge can transfer the badge IDs 1-5 to each other from Monday to Tuesday 12PM, but badges will be revokable by the manager.
* [**Incoming Approvals:** ](overview/how-it-works/transferability.md) In addition to having control over your outgoing transfers, have control over your incoming transfers via incoming approvals.
  * Ex: Block certain users from transferring to you. Block all transfers unless you opt-in to receiving them.
* [**Customizable Permissions**](overview/how-it-works/permissions.md)**:**  Enable customizable permissions for your collection, such as archiving the collection, deleting it, updating its metadata, updating transferability, etc.
  * Includes fine-grained controls such as when each permission can be executed? for what values? for what times?
* [**Time-Based Details**](for-developers/concepts/timelines.md)**:** Important collection details such as metadata are time-based, allowing you to automatically commit to updating it at a future time without needing to transact at that time.
  * Ex: Set the metadata to be one value from January 1 to January 10 and then auto-change to another value!

And much more!

## Distribution Tools

Distribution tools enable you to easily distribute badges to the correct owners based on your preferred method. We currently support the following but plan to support many more very soon!

* Password - Enter the correct password can claim a badge.
* Codes - Enter unique custom codes to claim a badge. Codes can be distributed however oyu would like (email, Twitter DMs, anything!).
* QR Codes - Restrict badges to be claimed by only those who scan a specific QR code.

See [Ecosystem](overview/ecosystem.md) for all the distribution tools we offer.

## Authentication Tools

Authentication tools enable you to verify users are who they say they are and/or verify they own specific badges!

**Blockin**

{% content-ref url="http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

With Blockin, you can restrict access to any website to only your badge holders!

Blockin is a universal, multi-chain sign-in interface that includes support for badge-gating websites. It extends Sign-In with Ethereum to support any and all blockchains.

**Other Authentication Tools**

See [Ecosystem](overview/ecosystem.md) for all the authentication tools we offer. In the near future, we plan to build and release tools to allow you to authenticate via the following methods:

* QR Codes
* Discord Bots
* Barcode Scanners
* Zero-Knowledge Proof of Ownerships
* Snapshots
* And more!

## Other Tools

* **Announcements** - To communicate with your badge holders, send an announcement which will be sent to all your badge owners via the BitBadges website's notifications.
* **Reviews** - Leave reviews on badge collections or specific users via the BitBadges website to let others know the reputation of the collection / user (e.g. scam? trustworthy? good experience?).

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

BitBadges Main Site - [https://bitbadges.org](https://bitbadges.org)

BitBadges App - [https://bitbadges.io](https://bitbadges.io)

GitHub - [https://github.com/bitbadges](https://github.com/bitbadges)

Project Board -  [https://github.com/bitbadges/projects](https://github.com/orgs/BitBadges/projects)

Discord - [https://discord.com/invite/TJMaEd9bar](https://discord.com/invite/TJMaEd9bar)

LinkedIn - [https://linkedin.com/company/bitbadges](https://linkedin.com/company/bitbadges)

Twitter - [https://twitter.com/BitBadges\_](https://twitter.com/BitBadges\_)

Facebook - [https://facebook.com/profile.php?id=100092259215026](https://facebook.com/profile.php?id=100092259215026)

Instagram - [https://instagram.com/bitbadges\_official/](https://instagram.com/bitbadges\_official/)

Slack - [https://bitbadges.slack.com/join/shared\_invite/zt-1tws89arl-TMSK\_4bdTLOLdyp177811Q#/shared-invite/email](https://bitbadges.slack.com/join/shared\_invite/zt-1tws89arl-TMSK\_4bdTLOLdyp177811Q#/shared-invite/email)

Crunchbase - [https://www.crunchbase.com/organization/bitbadges](https://www.crunchbase.com/organization/bitbadges)

Reddit -[ ](https://www.reddit.com/r/BitBadges/)[https://www.reddit.com/r/BitBadges/](https://www.reddit.com/r/BitBadges/)

Telegram - [https://t.me/BitBadges](https://t.me/BitBadges)


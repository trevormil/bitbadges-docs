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

We offer the full-stack of services you may need from the required storage (blockchain, data indexing, off-chain data storage) to tools for distributing badges to users via your preferred method to offline-first verification tools (in-person verification, website gated sign-ins) and more!

Additionally, the BitBadges app ([https://bitbadges.io](https://bitbadges.io)) allows you to show off your badges and build your digital identity as you collect more over time! On the flip side, you can browse others' badges to get a sense of their digital identity (e.g. are they a scammer or trustworthy?). This app has a social media feel but is also the all-in-one site to see any information about a user.

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
* Publications - Verify you are the author of a publication
* See more [here](broken-reference)!

## Improvements Over Existing Products

What makes our product better than existing products? Just to name a few:

* **Cross-Chain:** Supports users from multiple blockchain ecosystems instead of just one!
  * Currently, token standards are limited to one blockchain ecosystem (e.g. only Ethereum users or only Bitcoin users). This is a HUGE problem because most companies do not belong to a specific blockchain ecosystem, so it greatly restricts their potential userbase!
  * Ex: Imagine if a sporting event was limited to only Android users because the ticketing technology only worked on Android!
* **Decentralization:** We keep decentralization as a core principle, as opposed to some of our competitors who rely on more centralized architectures.
* **Scalability, Security, and Ease of Use:** Instead of having to write new custom code for every collection of tokens (as existing standards do which often results in security vulnerabilities), we use a registry architecture where code is reused by all collections. This results in everything being more scalable, easy to use, and battle-tested (see [here](overview/faq.md#what-makes-bitbadges-better-than-competitors)).
* [**Time-Based Balances**](overview/how-it-works/balances-transfers.md)**:** Time-based balances enables you to transfer a badge for only a specific period of time (e.g. subscription token for a month), clearly define token unlock schedules, or approve a transfer only for a specific period of time.&#x20;
* [**Off-Chain and Inherited Balances:**](overview/how-it-works/balances-types.md) We have created two new ways to store and track balances, in addition to the standard on-chain storage of balances. These can offer over 1000x better scalability and much better user experience.
  * 1\) Off-chain balances allow you to host your balances via a typical server, reducing the resources used by the blockchain by >99%.&#x20;
  * 2\) Inherited balances allow the owners of one badge to automatically be bounded to another.
* [**Batch Transfers**](overview/how-it-works/balances-transfers.md)**:** All accounting is done with badge ID ranges. Instead of needing 1000 transactions to send 1000 unique non-fungible badges (e.g. x1 of Badge ID 1, x1 of ID 2, ...), you can batch all into one transaction efficiently (e.g. send x1 of Badge IDs 1-1000).
* [**Fine-Grained Transferability Customization**](overview/how-it-works/transferability.md)**:** Existing standards simply abstract transferability to a simple "transferable" or "non-transferable". However, this is too simple for many use cases, such as those that require revoking badges, freezing transfers, etc.
  * We recognize that transferability is a complex protocol of who can transfer to who? at what times? what badges? how many? revokable? freezable? forceful transfers? or requires approval? what kind of approval? must own certain badges to be approved? And more!
  * Ex: Only those who own the verified badge can transfer the badge IDs 1-5 to each other from Monday to Tuesday 12PM.&#x20;
* [**Incoming Approvals:** ](overview/how-it-works/transferability.md) In addition to having control over your outgoing transfers, have control over your incoming transfers via incoming approvals.
  * Ex: Block certain users from transferring to you. Block all transfers unless you opt-in.
* [**Customizable Permissions**](overview/how-it-works/permissions.md)**:**  Enable customizable permissions for your collection, such as archiving the collection, deleting it, updating its metadata, updating transferability, etc.
* [**Time-Based Details**](for-developers/must-know-concepts/timelines.md)**:** Important collection details such as metadata are time-based, allowing you to automatically commit to updating it at a future time without needing to transact at that time.

And much more!

## Core Ideas

What are the core ideas and principles behind BitBadges?

* **Rapidly-Evolving**:  Create a single, dynamic token standard that rapidly evolves and improves over time.
  * Token standards should be flexible and continuously adding new functionality, as opposed to existing ones which are rigid, not adaptable, and hard to adopt new ones.
* **Cross-Chain**: Tokens should support users from ANY blockchain
* **Option of Decentralization:** Users should always have an option to interact with BitBadges in a trustless, decentralized manner. However, but we recognize that >90% of users don't care about decentralization.&#x20;
  * We offer functionality that sacrifices decentralization for user experience and scalability, but the option for full decentralization and trustless interaction will always be there.
* **User-Friendly**: Prioritize user experience over everything else.&#x20;
  * Users should not even need to know what a blockchain is or that they are using a blockchain. It should be as simple as browsing a typical website.
* **Community-Driven Ecosystem:** Make developing within the BitBadges ecosystem as easy as possible.
  * Long term, the goal is to have an ecosystem of thousands of community-built projects and tools for interacting with BitBadges.
* **Generic:** Design everything in a generic way that supports universality
* **Open-Source:** All code that makes BitBadges work will be open-sourced for public review

## Products

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

{% content-ref url="http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](http://127.0.0.1:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

Blockin is a universal, multi-chain sign-in interface for Web 3.0 that includes support for badge-gating websites. While Blockin is not an official BitBadges product, Blockin was co-created and is maintained by one of the BitBadges founders, trevormil.eth.&#x20;

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


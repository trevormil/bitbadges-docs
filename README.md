---
description: >-
  Here, you will find documentation about BitBadges, how it works, how to
  interact, and how to contribute!
---

# 👋 BitBadges Overview

<figure><img src=".gitbook/assets/bitbadges_letters.png" alt=""><figcaption></figcaption></figure>

## Overview&#x20;

Digital tokens (badges) are simply something that you can own digitally and prove ownership of it. You probably already use and own many digital tokens: verification checkmarks, a movie streaming subscription, concert tickets, etc. These tokens can be used for infinitely many [use cases](./#use-cases), each potentially offering you different utility and value. Some may have real-world use cases (e.g. entry to a concert), and some may be purely digital (e.g. verification checkmark).&#x20;

When combined with blockchain technology, they become even more secure and more powerful, due to the unique properties of the blockchain ([read more here](https://101blockchains.com/advantages-of-nfts/)).&#x20;

**BitBadges can be described as tokenization-as-a-service. We offer an open-source, offline-first, community-driven suite of tools that enable you to create, customize, verify, and integrate with digital blockchain tokens for any use case that you desire.**&#x20;

We offer all services you may need from the required storage (blockchain, data indexing, off-chain data storage) to distributing to users via your preferred method to verification tools (in-person verification, website gated sign-ins) and more!

Additionally, the BitBadges app ([https://bitbadges.io](https://bitbadges.io)) allows you to show off your badges and build your digital identity as you collect more over time! On the flip side, you can browse others' badges to get a sense of their digital identity (e.g. a scammer or trustworthy?). We aim for this app to have a social media feel but also be the all-in-one site to see any information about a user.

To learn more about BitBadges (the company), visit [https://bitbadges.org](https://bitbadges.org).

## Use Cases

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

* Attendance Badges - Create an attendance badge for an event or trip as a souvenir!
* Memberships/Subscriptions/Access Tokens - Gym memberships, website subscriptions, etc
* Premium Content - Offer premium features to badge owners
* Recognition of Achievement or Completion - Job certifications, awards, athletic accomplishments, etc.
* E-Learning - Gamify the learning experience through learning badges
* University Diplomas - Universities can offer verifiable diplomas to students as a badge.
* Publications - Verify you are the author of a publication
* See more [here](broken-reference)!

## Core Ideas

What are the core ideas and principles behind BitBadges?

* **Rapidly-Evolving**:  Create a single, dynamic token standard that rapidly evolves over time.
  * Token standards should be flexible and continuously adding new functionality, as opposed to existing ones which are rigid and not easily adaptable.&#x20;
  * We will iterate fast and continuously add new features
* **Cross-Chain**: Develop a token standard which supports users from ANY blockchain
  * Currently, token standards are limited to one blockchain ecosystem (e.g. only Ethereum users or only Bitcoin users). This is a problem because most companies do not belong to a specific blockchain ecosystem, so it restricts their potential userbase.
  * Ex: Imagine if a sporting event was limited to only Android users!
* **Option for Decentralization:** Users should always have an option to interact with BitBadges in a trustless, decentralized manner, but we recognize that 90% of users don't care about decentralization. We offer semi-centralized solutions to enhance the user experience, but the option for full decentralization is always an option.
* **User-Friendly**: Prioritize user experience over everything else.&#x20;
  * Users should not even need to know what a blockchain is or that they are using a blockchain. It should be as simple as browsing a typical website.
* **Ecosystem:** Make developing on top of BitBadges as easy as possible.
  * Long term, the goal is to have an ecosystem of thousands of community-built projects and tools for interacting with BitBadges.
  * Contributions are also greatly encouraged and appreciated
* **Generic:** Design everything in a generic way that supports universality
* **Open-Source:** All code that makes BitBadges work will be open-sourced for public review

## How Are We Better?

What makes our token standard better than existing blockchain standards and competitors?

Just to name a few:

* **Cross-Chain:** Supports users from multiple blockchain ecosystems instead of just one
* **Reused and Battle-Tested Code:** Instead of having to write new custom code for every collection of tokens (as existing standards do), we reuse code to make our standard much more scalable and battle-tested.
* **Balance Types:** We have created two new scalable and powerful ways to store and track balances, in addition to the standard on-chain storage of balances.&#x20;
  * 1\) Off-chain balances allow you to host your balances via a typical server, reducing the resources used by the blockchain.&#x20;
  * 2\) Inherited balances allow the owners of one badge to automatically own another.
* **ID Ranges:** All balances accounting is done with ID ranges. Instead of needing 1000 transactions to send 1000 unique non-fungible badges (e.g. x1 of Badge ID 1, ...), you can batch all into one transaction efficiently (x1 of Badge IDs 1-1000).
* **Time-Based Accounting:** All balances are time-based. This enables you to transfer a badge for only a specific period of time (e.g. subscription token for a month), clearly define token unlock schedules, or approve a transfer only for a specific period of time.&#x20;
* **Time-Based Metadata:** All metadata is time-based, allowing you to automatically update it at a certain time without transacting with a blockchain.
* **Transferability Customization:** Existing standards simply abstract transferability to a simple "transferable" or "non-transferable". However, we recognize that transferability is a complex protocol of who can transfer to who? at what times? what badges? how many? revokable? freezable? forceful transfers? or requires approval?  We handle all of this.
  * Additionally, we have a feature to only allow transfers if you own (or don't own) badges of another collection! Ex: Only those who own the verified badge can transfer. Or, only those who do not own a scammer badge can transfer and interact with your collection.&#x20;
* **Collection and User-Level Approvals:** We define three levels of approvals (collection-wide, incoming, and outgoing).&#x20;
  * For a transfer to be successful, it must be approved by the collection-wide approvals, by the sender's outgoing approvals, and by the recipient's incoming approvals.&#x20;
  * This allows us to clearly define the transferability and enable many neat features like revokable badges, freezing badges, opt-in only collections, and more!
* **Customizable Permissions:** You can optionally specify to have customizable permissions for your collection, such as archiving the collection, deleting it, updating its metadata, updating transferability, etc
* **Decentralization:** We keep decentralization as a core principle, as opposed to some of our competitors.

And much more coming soon!

## Products

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

BitBadges offers an L1 delegated proof-of-stake blockchain built with Cosmos SDK that enables the natively cross-chain issuance of digital tokens (e.g. an Ethereum user can seamlessly issue a badge to a Cosmos user). The blockchain is able to attain instant transaction finality using Tendermint and natively supports users from multiple Layer 1 blockchains (Ethereum, Cosmos) via IBC and account mappings.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

BitBadges provides their own website to view and interact with the BitBadges blockchain at [https://bitbadges.io](https://bitbadges.io). This site aims to be the all-in-one site for any crypto user from any blockchain (see a user's badges, digital collectibles, transaction history, reviews, etc all on one site).

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

The BitBadges Indexer is a tool used to fetch the latest blockchain information and store any additional data that is needed (e.g. user activity, metadata, etc).

The BitBadges team runs their own indexer and provides a user-friendly developer API to allow developers to build on top of it with, but you may also run and customize your own indexer.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

The BitBadges SDK is a TypeScript library that provides all the tools you need to develop on top of BitBadges.

{% content-ref url="http://localhost:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](http://localhost:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

Blockin is a universal, multi-chain sign-in interface for Web 3.0. While Blockin is not an official BitBadges product, Blockin was co-created and is maintained by one of the BitBadges founders, trevormil.eth.&#x20;

Blockin plans to integrate many features using BitBadges, such as badge-gating sign-ins for websites.

{% content-ref url="for-developers/need-to-know/standards.md" %}
[standards.md](for-developers/need-to-know/standards.md)
{% endcontent-ref %}

We are decentralizing the process of proposing / creating token and metadata standards ([Standards](for-developers/need-to-know/standards.md)). Have an idea for a new token standard? Propose it! Standards can define anything from the expected permissions, transferability, or even how metadata is interpreted.

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


# 🏆 How It Works

See the subpages for further explanations on specific topics.

See the [For Developers](broken-reference) section for technical explanations.

## Creation

Collections are the core of BitBadges. A collection consists of one or more badges, where each badge in the collection is referenced by a unique numeric ID, starting at 1 and incrementing.

When creating a badge collection, the creator can customize the following properties however they would like:

[**Collection Metadata**](page-1.md)**:** What is the collection's name, description, image, category etc?

[**Badge Metadata**](page-1.md)**:** For each badge, what is its name description, image, category, etc?

[**Supply**](total-supplys.md)**:** How much should the total supply of each badge be? (e.g. badge ID #1 may have a supply of x100 whereas badge ID #2 only has x1)

[**Balances Types**](balances-types.md) **-** How do you want to store your balances? On the blockchain? Off-chain? Or inherit  all owners from other badge(s)?

[**Transferability**](transferability.md)**:** Transferability defines the rules for transferring badges within the collection.&#x20;

At its simplest, a collection can be transferable (badges can be transferred freely from one owner to another) or non-transferable (once a badge is owned, it is tied to that owner).

We also offer many customization options such as making badges revokable from owners, freezing an owner's ability to transfer, restricting who can send to who, restricting when users can transfer, and more!

[**Manager**](manager.md)**:** Every collection will have a manager. Upon creation, it will be the creator of the collection. The manager can optionally be granted certain permissions seen directly below.

[**Permissions**](permissions.md)**:** The manager is able to execute the following permissions:

* Add / create badges to the collection?
* Update the transferability?
* Delete the collection?
* Archive the collection?
* Transfer the manager role?
* Update the collection details (such as metadata)?

[**Standard**](standards.md)**:** Each collection must define what standard it uses to explain how to interpret the details of the badge. For developers, see [standards](../../for-developers/need-to-know/standards.md).

## Distribution&#x20;

Once the collection is created, the badges then have to be distributed to owners. This can be done via your preferred method. Some examples currently are via **password, QR codes, e-mails, whitelists, first-come first-serve, manually transfering to each recipient, etc.**

See [Ecosystem ](../ecosystem.md)to find a list of all distribution tools built by the BitBadges team and community.

One of the core ideas behind BitBadges is we want to decentralize the distribution process across hundreds or thousands of community-built distribution tools. See [this tutorial](../../for-developers/tutorials/build-a-distribution-tool.md) if you would like to create and add a new tool.

## Indexing / Verification&#x20;

For browsing the current state and owners of a badge collection, the most simple way to do by visiting it on the BitBadges website ([https://bitbadges.io](https://bitbadges.io)).

There are also many community-built tools for verifying badge ownership for different use cases (websites, in-person events, etc) and indexing of collections, which can be found at [Ecosystem](../ecosystem.md).

## Rest of Lifetime

After the initial creation and distribution of badges, the collection will live out according to its rules and permissions set.

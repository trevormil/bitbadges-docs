# 🏆 How It Works

**Pre-Readings**

It is assumed that you have a basic understanding of how Web3 and blockchains work from a high-level user perspective (what an address is, what a transaction is, what a wallet is, etc).

If not, we recommend first familiarizing yourself with these terms. There are plenty of resources available online.

**Further Readings**

See the subpages for further in-depth explanations.

See the [For Developers](broken-reference) section for technical explanations.

## Creation

Collections are the core of BitBadges. All collections are identified by a numeric ID, and each badge within the collection is also referenced by a unique numeric ID, starting at 1.

So for example, collection #1 can have 100 badges which are identified #1 through #100.

**Customization Options**

When creating a badge collection, the creator can customize the following properties however they would like:

[**Collection Metadata**](page-1.md)**:** What is the collection's name, description, image, category, etc?

[**Badge Metadata**](page-1.md)**:** For each badge, what is its name description, image, category, etc?

[**Supply**](total-supplys.md)**:** How much should the total supply of each badge within the collection be? (e.g. badge ID #1 may have a supply of x100 whereas badge ID #2 only has x1)

[**Balances Types**](balances-types.md) **-** How do you want to store your balances? On the blockchain? Off-chain? Or inherit the owners from other badge(s)?

[**Transferability**](transferability.md)**:** Transferability defines the rules for transferring badges within the collection.&#x20;

At its simplest, a collection can be transferable (badges can be transferred freely from one owner to another) or non-transferable (once a badge is owned, it is tied to that owner).

We also offer many customization options such as making badges revokable from owners, freezing an owner's ability to transfer, restricting who can send to who, restricting when users can transfer, and more!

[**Manager**](manager.md)**:** Every collection will have a manager. Upon creation, it will be the creator of the collection. The manager can optionally be granted certain permissions seen directly below.

[**Permissions**](manager.md)**:** The manager can optionally have the privilege to execute the following:

* Add / create badges to the collection?
* Update the transferability?
* Delete the collection?
* Archive the collection?
* Transfer the manager role?
* Update the collection details (such as metadata)?

Each permission has fine-grained controls, such as when it can be executed vs forbidden, for which badges, etc.

[**Standard**](standards.md)**:** Each collection must define what standard it uses to explain how to interpret the details of the badge. For developers, see [standards](../../for-developers/concepts/standards.md).

## Distribution&#x20;

Once the collection is created, the badges then have to be distributed to owners. This can be done via your preferred method. Some examples currently are via **password, QR codes, e-mails, whitelists, first-come first-serve, manually transfering to each recipient, etc.**&#x20;

BitBadges is cross-chain meaning that any blockchain user from one ecosystem (e.g. Ethereum) can receive and send badges to and from users from another ecosystem (e.g. Cosmos).

One of the core ideas behind BitBadges is we want to decentralize the distribution process across hundreds or thousands of community-built distribution tools. See [this tutorial](../../for-developers/tutorials/build-a-distribution-tool.md) if you would like to create and add a new tool.

See [Ecosystem ](../ecosystem.md)to find a list of all distribution tools built by the BitBadges team and community.

## Indexing / Verification&#x20;

For browsing the current state and owners of a badge collection, the most simple way to do by visiting it on the BitBadges website ([https://bitbadges.io](https://bitbadges.io)).

There are also many community-built tools for verifying badge ownership for different use cases (websites, in-person events, etc) and indexing of collections, which can be found at [Ecosystem](../ecosystem.md).

## Rest of Lifetime

After the initial creation and distribution of badges, the collection will live out according to its rules and permissions set.

# Badges vs Address Lists

There are two ways you can create using BItBadges. The easiest way to create is by visiting [https://bitbadges.io/collections/mint](https://bitbadges.io/collections/mint).

## **Badges**

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

Badges are blockchain tokens that can be owned with customization options like supply, transferability, and more! These are more complex than address lists but also are much more customizable.

The first step in creating badges is to create a collection. Collections are the core of BitBadges. All collections are identified by a numeric ID, and each badge within the collection is also referenced by a unique numeric ID, starting at 1. So for example, collection #1 can have 100 badges which are identified #1 through #100.

**Customization Options**

When creating a badge collection, the creator can customize the following properties however they would like:

[**Collection Metadata**](metadata.md)**:** What is the collection's name, description, image, category, etc?

[**Badge Metadata**](metadata.md)**:** For each badge, what is its name description, image, category, etc?

[**Supply**](total-supplys.md)**:** How much should the total supply of each badge within the collection be? (e.g. badge ID #1 may have a supply of x100 whereas badge ID #2 only has x1)

[**Balances Types**](balances-types.md) **-** How do you want to store your balances? On the blockchain? Off-chain? Or inherit the owners from other badge(s)?

[**Transferability**](transferability.md)**:** Transferability defines the rules for transferring badges within the collection.&#x20;

At its simplest, a collection can be transferable (badges can be transferred freely from one owner to another) or non-transferable (once a badge is owned, it is tied to that owner).

We also offer many customization options such as making badges revokable from owners, freezing an owner's ability to transfer, restricting who can send to who, restricting when users can transfer, and more!

Transferability also encompasses how the badges are distributed from the Mint address (codes, passwords, QR codes, first-come first-serve), etc.

[**Manager**](manager.md)**:** Every collection can have a manager which can optionally be granted certain admin permissions. Admin permissions can be on-chain (like seen below), or you can create your own utility for the Manager.&#x20;

For example, the BitBadges website (off-chain) only allows announcements to be sent for a collection from the manager.

[**Permissions**](manager.md)**:** The manager can optionally have the privilege to execute permissions such as:

* Add / create badges to the collection?
* Update the transferability?
* Delete the collection?
* Archive the collection?
* Transfer the manager role?
* Update the collection details (such as metadata)?

Each permission has fine-grained controls, such as when it can be executed vs forbidden, for which badges, etc. See a full list [here](manager.md).

[**Standard**](standards.md)**:** Each collection can define what standard it uses to explain how to interpret the details of the badge. For example, should we expect it to follow a specific format?

For developers, see [standards](../../for-developers/core-concepts/standards.md).

## Address Lists

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

Address lists are simply a list of users identified by a unique ID. Lists are less complex because you do not need to deal with all the added complexity of tokens (badges) such as supplys, who owns what badge?, permissions, transferability, etc.&#x20;

Lists are provided as for convenience and simplicity, but note that all lists can be created as a badge collection where users own x1 of a badge to be "on the list".

**Storage:** Address lists can be stored on-chain or off-chain (centralized servers). Off-chain lists are updatable and deletable (with other options) whereas on-chain lists must be permanently frozen (not updatable or deletable).&#x20;

**Metadata:** Lists can be customized with metadata like a name, image, description, etc. They will show up on users' profiles under the lists' category.

**All Except:** Lists are invertible meaning your list can denote whether to only specify certain addresses or include all addresses but certain addresses (whitelist or blacklist).
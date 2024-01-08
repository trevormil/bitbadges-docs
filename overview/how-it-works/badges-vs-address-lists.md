# Badges vs Address Lists

There are two ways you can create using BItBadges. Visit [https://bitbadges.io/collections/mint](https://bitbadges.io/collections/mint) to get started.

## **Badges**

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Badges are blockchain tokens that can be owned with customization options like supply, transferability, and more! These are more complex than address lists but also are much more customizable.

The first step in creating badges is to create a collection. Collections are the core of BitBadges. All collections are identified by a numeric ID, and each badge within the collection is also referenced by a unique numeric ID, starting at 1. So for example, collection #1 can have 100 badges which are identified #1 through #100.

**Customization Options**

When creating a badge collection, the creator can customize the following properties however they would like:

[**Collection Metadata**](metadata.md)**:** What is the collection's name, description, image, category, etc?

[**Badge Metadata**](metadata.md)**:** For each badge, what is its name description, image, category, etc?

[**Supply**](total-supplys.md)**:** How much should the total supply of each badge within the collection be? (e.g. badge ID #1 may have a supply of x100 whereas badge ID #2 only has x1)

[**Balances Types**](balances-types.md) **-** How do you want to store your balances? On the blockchain? Off-chain?

[**Transferability**](transferability.md)**:** Transferability defines the rules for transferring badges within the collection.&#x20;

At its simplest, a collection can be transferable (badges can be transferred freely from one owner to another) or non-transferable (once a badge is owned, it is tied to that owner).

We also offer many customization options such as making badges revokable from owners, freezing an owner's ability to transfer, restricting who can send to who, restricting when users can transfer, and more!

Transferability also encompasses how the badges are distributed from the Mint address (codes, passwords, QR codes, first-come first-serve), etc.

[**Manager**](manager.md)**:** Every collection can have a manager which can optionally be granted certain admin permissions.Each permission has fine-grained controls, such as when it can be executed vs forbidden, for which badges, etc. See a full list [here](manager.md).

[**Standard**](standards.md)**:** Each collection can define what standard it uses to explain how to interpret the details of the badge. For example, should we expect it to follow a specific format?

## Address Lists

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Address lists are simply a list of users identified by a unique ID. Lists are less complex because you do not need to deal with all the added complexity of tokens (badges) such as supplys, who owns what badge?, permissions, transferability, etc.&#x20;

**Storage:** Address lists can be stored on-chain or off-chain (centralized servers). Off-chain lists are updatable and deletable (with other options) whereas on-chain lists must be permanently frozen (not updatable or deletable).&#x20;

**Metadata:** Lists can be customized with metadata like a name, image, description, etc. They will show up on users' profiles under the lists' category.

**Whitelists and Blacklists:** Lists are invertible meaning your list can denote whether to only specify certain addresses or include all addresses but certain addresses (whitelist or blacklist).

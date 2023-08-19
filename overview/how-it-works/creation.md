# Creation

The first step in creating badges is to create a collection. Collections are the core of BitBadges. All collections are identified by a numeric ID, and each badge within the collection is also referenced by a unique numeric ID, starting at 1. So for example, collection #1 can have 100 badges which are identified #1 through #100.



**Customization Options**

When creating a badge collection, the creator can customize the following properties however they would like:

[**Collection Metadata**](../concepts/metadata.md)**:** What is the collection's name, description, image, category, etc?

[**Badge Metadata**](../concepts/metadata.md)**:** For each badge, what is its name description, image, category, etc?

[**Supply**](../concepts/total-supplys.md)**:** How much should the total supply of each badge within the collection be? (e.g. badge ID #1 may have a supply of x100 whereas badge ID #2 only has x1)

[**Balances Types**](../concepts/balances-types.md) **-** How do you want to store your balances? On the blockchain? Off-chain? Or inherit the owners from other badge(s)?

[**Transferability**](../concepts/transferability.md)**:** Transferability defines the rules for transferring badges within the collection.&#x20;

At its simplest, a collection can be transferable (badges can be transferred freely from one owner to another) or non-transferable (once a badge is owned, it is tied to that owner).

We also offer many customization options such as making badges revokable from owners, freezing an owner's ability to transfer, restricting who can send to who, restricting when users can transfer, and more!

[**Manager**](../concepts/manager.md)**:** Every collection will have a manager. Upon creation, it will be the creator of the collection. The manager can optionally be granted certain permissions seen directly below.

[**Permissions**](../concepts/manager.md)**:** The manager can optionally have the privilege to execute the following:

* Add / create badges to the collection?
* Update the transferability?
* Delete the collection?
* Archive the collection?
* Transfer the manager role?
* Update the collection details (such as metadata)?

Each permission has fine-grained controls, such as when it can be executed vs forbidden, for which badges, etc.

[**Standard**](../concepts/standards.md)**:** Each collection must define what standard it uses to explain how to interpret the details of the badge. For developers, see [standards](../../for-developers/concepts/standards.md).

# 🏆 How It Works

See the [For Developers](broken-reference) section for further technical explanations.

## Badge Creation

Collections are the core of BitBadges. A collection consists of one or more badges, where each badge in the collection is referenced by a unique numeric ID, starting at 1 and incrementing.

When creating a badge collection, the creator can customize the following properties however they would like:

**Collection Metadata:** What is the collection's name, description, image, category etc?

**Badge Metadata:** For each badge, what is its name description, image, category, etc?

**Supply:** How much should the total supply of each badge be? (e.g. badge ID #1 may have a supply of x100 whereas badge ID #2 only has x1)

**Balances Type -** How do you want to store your balances? On the blockchain? Off-chain? Or inherit  all owners from other badge(s)?

**Transferability:** Transferability defines the rules for transferring badges within the collection.&#x20;

At its simplest, a collection can be transferable (badges can be transferred freely from one owner to another) or non-transferable (once a badge is owned, it is tied to that owner).

We also offer many customization options such as making badges revokable from owners, freezing an owner's ability to transfer, restricting who can send to who, restricting when users can transfer, and more!

**Manager:** Every collection will have a manager. Upon creation, it will be the creator of the collection. The manager can optionally be granted certain permissions seen directly below.

**Permissions:** The manager is able to execute the following permissions:

* Add / create badges to the collection?
* Update the transferability?
* Delete the collection?
* Archive the collection?
* Transfer the manager role?
* Update the collection details (such as metadata)?

**Standard:** Each collection must define what standard it uses to explain how to interpret the details of the badge. For developers, see [standards](../../for-developers/need-to-know/standards.md).

## Badge Distribution&#x20;

Once the collection is created, the badges then have to be distributed to owners. This can be done via your preferred method. Some examples currently are via password, QR codes, e-mails, whitelists, first-come first-serve, manually transfering to each recipient, etc.

See [Ecosystem ](../ecosystem.md)to find a list of all distribution tools built by the BitBadges team and community.

One of the core ideas behind BitBadges is we want to decentralize the distribution process across hundreds or thousands of community-built distribution tools. See [this tutorial](../../for-developers/tutorials/build-a-distribution-tool.md) if you would like to create and add a new tool.

## Indexing / Verification&#x20;

At some point, you will need fetch the current details of the badge collection and / or the current owners (for verification, showing badges off, etc). The most simple way to do by visiting it on the BitBadges website ([https://bitbadges.io](https://bitbadges.io)).

In some scenarios, visiting the BitBadges website is not the most ideal. An example is if you are in a place with no Internet or no access to computers (e.g. the gate of a venue). Check [Ecosystem ](../ecosystem.md)to find a list of verification tools. An example includes restricting website access to only badge owners.

## Rest of Lifetime

After the initial creation and distribution of badges, the collection will live out according to its rules. If users can transfer badges, transfers will be allowed. If the manager can archive the collection, they can archive it. And so on.

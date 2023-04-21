# 🏆 How It Works

Note this is a non-technical explanation. See the [For Developers](broken-reference) section for further technical explanations.

### Badge Collections

Collections are the core concept of BitBadges. Collections can contain anywhere from 1 to 2^64 - 1 badges.&#x20;

All collections will be assigned a collection ID number, and all badges within each collection will be assigned a badge ID number. IDs for both badges and collections start at 1, not 0 (sorry developers :smile:).&#x20;

For example, a specific badge can be identified as badge #2 in collection #9.

**Metadata:** Each collection and each badge will have its own corresponding metadata which describes it (title, description, image, etc).

**Supply:** Each badge will have a fixed supply. This supply cannot be edited once the badge is created.&#x20;

**Transferability:** All badges in a collection will share the same transferability. Transferable badges can be sent between users. Non-transferable badges are locked to an account.

**Manager:** All collections must specify a user to be its manager. The manager is responsible for the distribution of badges and can also be granted special privileges, if desired.&#x20;

These privileges include:

* CanDelete - Can the manager delete the collection?
* CanUpdateBytes - Can the manager update the bytes field?
* CanManagerBeTransferred - Can the role of the manager be transferred?
* CanUpdateUris - Can the manager update the metadata URIs?
* CanCreateMoreBadges - Can the manager add badges to the collection?
* CanUpdateDisallowed - Can the manager update the transferability?

If enabled, these permissions can be disabled. However, once disabled, a permission can **never** be turned back on.

**Standard:** Each badge must define what standard it uses. This lets others know how to interpret the details of the badge. For developers, see [standards](../for-developers/need-to-know/standards.md).

### Distributing Badges

Badges can be either distributed (aka minted) manually or via a claiming process. Note that if a badge is non-transferable, the recipient of the minted badge will not be able to transfer it.

**Manual distribution** means that badges are transferred directly to a users' account. The sender of the badge (the manager) will pay all transaction fees, so this is typically only used with a small number of recipients.

**Claims** allow users to claim badges if certain criteria is met. Currently, on the BitBadges website, the following distribution methods for claims are supported:

* **First Come, First Serve** - No criteria. Anyone who wants a badge can claim. Limit one claim per address.
* **Whitelist** - Only specific addresses are able to claim.
* **Codes / Password** - Generate a password or unique codes that can be entered to redeem the badge.
  * The codes and passwords can be distributed to the claimees by URL, QR codes, or manually.
  * Note that on the BitBadges website, codes and passwords are stored in a centralized manner for a better user experience.


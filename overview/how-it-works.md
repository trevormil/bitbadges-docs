# 🏆 How It Works

See the [For Developers](broken-reference) section for further technical explanations.

## Badge Collections

Collections are the core concept of BitBadges. A collection consists of one or more badges, where each badge is its own token and can have its own unique metadata (title, description, image, etc) and properties (supply).&#x20;

Ex: A university can create a collection where each badge corresponds to the completion of a course.

### **Customization**

How can badge collections be customized?

**Metadata -** Collections and each individual badge can have unique metadata (title, description, image, etc).

**Supply** - Different badges in a collection can have a different total supply (e.g. badge ID #1 may have a supply of x100 whereas badge ID #2 only has x1).&#x20;

**Transferability -** All badges in a collection will share the same transferability. You can make a badge transferable which means it can be sent between users. Or, you can make it non-transferable where it is locked to an account. Or, you can pick and choose which addresses can send / receive badges.

**Permissions -** All collections must specify a user to be its manager. The manager can be granted special privileges, if desired.&#x20;

These privileges include:

* Can the manager add badges to the collection?
* Can the manager update the transferability?
* Can the manager delete the collection?
* Can the role of the manager be transferred?
* Can the manager update the bytes field?
* Can the manager update the metadata?

Once a permission is turned off (disabled), it can **never** be turned back on.&#x20;

**Standard:** Each badge must define what standard it uses which allows you to customize how to interpret the details of the badge. For developers, see [standards](../for-developers/need-to-know/standards.md).

### Distributing Badges (Minting)

Badges can be either distributed manually or via a claiming process.

**Manual transfers** means that badges are transferred directly to a users' account by the manager. The manager will pay all transaction fees, so this is typically only used with a small number of recipients.

**Claims** allow users to claim badges if certain criteria is met. Currently, on the BitBadges website, the following distribution methods for claims are supported:

* **First Come, First Serve** - No criteria. Anyone who wants a badge can claim. Limit one claim per address.
* **Whitelist** - Only specific addresses are able to claim.
* **Codes / Password** - Generate a password or unique codes that can be entered to redeem the badge.
  * The codes and passwords can be distributed to the claimees by URL, QR codes, or manually.
  * Note that on the BitBadges website, codes and passwords are stored in a centralized manner for a better user experience.
* We are looking to add more distribution methods in the future!

#### Distributing vs Transferring

Distribution or minting badges refers to the process of the initial allocation of the badges. Transferring means that the badges have already been distributed and are changing ownership.

Note that a collection's transferability does not apply to the distribution process. If badges in a collection are non-transferable, the recipient of the minted badge will not be able to transfer it, but the initial distribution will not be blocked.

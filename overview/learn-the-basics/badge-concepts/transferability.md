# Transferability

The transferability defines the rules for transferring badges within the collection. Note this does not apply to address lists or any off-chain balances type ([see here](balances-types.md)), only standard on-chain balances.

### **Transferable vs Non-Transferable**

At its simplest, a collection can be thought of as transferable (badges can be transferred freely from one owner to another) or non-transferable (once a badge is owned, it is tied to that owner and never transferable).

### Problem

However, only specifying simply "transferable" vs "non-transferable" is very naive and not suitable for many use cases.&#x20;

For example, what if you need to be able to revoke? Freeze one's ability to transfer? Restrict who can transfer to who? Restrict when users can transfer? Restrict how many times a transfer can occur? Restrict the total amount of badges transferred? Or a combination of all of these?

We abstract everything to a clearly defined interface that accounts for all these factors on three different levels.

<figure><img src="../../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

## Approval Levels

We define three levels of approved transfers: collection-wide, incoming, and outgoing.&#x20;

<figure><img src="../../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

### **Collection Approvals**

The collection-wide approved transfers defines all possible combinations of transfers that are allowed to take place. **All transfers must be explicitly approved on the collection level.** This is established upon creation and updated by the manager (according to the permissions set).

For example, the manager could define all badges to be transferable, non-transferable, revokable by the manager. Or, they can specify challenges that must be passed in order to transfer (e.g. you must own this badge to interact with this collection or you must not own a scammer badge to interact with this collection). This also encompasses transfers from the Mint address. See all possibilities below.

The collection-wide approved transfers are unique because there is a forceful option which allows you to override and ignore the other two levels of approvals (incoming and outgoing). This is used for forcefully revoking badges or forcefully freezing badges. **If it does not override the incoming and outgoing levels of approvals, the transfer must also be approved on those levels as well.**

### **Outgoing Approvals**

The outgoing approvals are the approved transfers of the sender. By default, all transfers where the sender equals the transaction initiator are allowed, and all others are disallowed. The sender can optionally approve other addresses to transfer on their behalf. If you are familiar with other blockchain NFTs and tokens, this is the same as those approvals.

The collection can define a default outgoing approved transfers for each user. This can then be updated by the user as they desire.

### **Incoming Approvals**

The incoming approvals are the approved transfers of the recipient. By default, all transfers where the recipient equals the transaction initiator are allowed, and all others are disallowed. The recipient can choose whether to block or allow incoming transfers via their incoming approved transfers. This is a new concept introduced by BitBadges.

The collection can define a default incoming approved transfers for each user. They can then be updated by the user as they desire. They can then be updated by the user as they desire.

### Transfer Validation Scenario

Let's delve into a transfer scenario to understand the process of approval validation:

#### Scenario: Bob transfers x5 of Badge IDs 1-10 to Alice for January to March&#x20;

1. **Collection-Level Approval Check**:
   * The initial step involves verifying if the transfer adheres to collection-level rules. For instance, if Badge ID 1 is found to be non-transferable within the collection, the transfer attempt would be deemed unsuccessful.
2. **Incoming Approval Check**:
   * If the transfer passes the collection-level check, the subsequent step involves assessing Alice's incoming approvals. This evaluation considers whether Alice has blocked Bob from sending her badges and whether she has opted in to the specific badge collection in question.
3. **Outgoing Approval Check**:
   * Upon Alice's incoming approval, the process moves on to Bob's approvals. It's necessary to ascertain whether Bob has provided his consent for the transfer to proceed. This step is particularly significant if the transfer was initiated by a party other than Bob himself.

This layered approach to approval ensures a thorough examination of the transfer's legitimacy and compliance with various levels of permissions. The hierarchical structure prevents unauthorized transfers and enhances transparency in the transfer process.

### Customization Options

At each level, we offer the following functionality for defining approved transfers. Mix and match any combinations:

* Who can transfer to who? And who can initiate the transaction?
* When can the transfer take place?
* Which badges can be sent? For how long ([see ownership times](time-dependent-ownership.md))? What amount?
* Max number of overall transfers? Max per sender? Max per recipient? Max per initiator?
* Max amount transferred? Max per sender? Max per recipient? Max per initiator?
* Predetermined transfers?
  * Transfer A must take place before Transfer B before Transfer C
* Incremented transfers?&#x20;
  * Start with specific badges and ownership times and increment them every transaction.
  * Ex: Transfer x1 of Badge ID 1, then x1 of Badge ID 2, and so on...
* Must own (or not own) specific badges to be approved
  * Ex: Must own a membership to transfer or must now own a scammer badge to transfer
* Require sender to be the initiator? Require sender to not be the initiator?
* Require recipient to be the initiator? Require recipient to not be the initiator?
* And more!

### **Updatability**

We also support fine-grained updatability for all combinations of the above functionality through the manager permissions.

For example, the manager can permanently lock all combinations on a collection-level to make the transferability non-updatable. Or, they can lock it for a certain amount of time. Or, they can keep it updatable. One can even combine combinations, such as locking an approval for a specific badge for a specific time for specific users.

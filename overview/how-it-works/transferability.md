# Transferability

The transferability defines the rules for transferring badges within the collection. Note this does not apply to the off-chain balances type because balances are completely controlled by the manager off-chain ([see here](balances-types.md)).

### **Transferable vs Non-Transferable**

At its simplest, a collection can be transferable (badges can be transferred freely from one owner to another) or non-transferable (once a badge is owned, it is tied to that owner and never transferable).

### Problem

However, only specifying simply "transferable" vs "non-transferable" is very naïve and not suitable for many use cases.&#x20;

For example, what if you need to be able to revoke forcefully? Freeze one's badges? Restrict who can transfer to who? Restrict when users can transfer? Restrict how many transfers? Restrict the total amount of badges transferred? Or a combination of all of these?

We aim to abstract everything to a clearly defined interface that accounts for all these factors on three different levels.

### Approval Levels

We define three levels of approved transfers: collection, incoming, and outgoing.&#x20;

**Collection-Wide**

The collection-wide approved transfers defines all possible combinations of transfers that are allowed to take place. **All transfers must be explicitly approved on the collection level.** This is defined and updated by the manager (according to the permissions set).

For example, you could define badges to be transferable, non-transferable, revokable by the manager. You can specify challenges that must be passed in order to transfer (e.g. you must own this badge to interact with this collection or you must not own a scammer badge to interact with this collection). See all possibilities below.

The collection-wide approved transfers are unique because there is a forceful option which allows you to override and ignore the other two levels of approvals (incoming and outgoing). This is used for forcefully revoking badges or forcefully freezing badges. **If it does not override the incoming and outgoing levels of approvals, the transfer must also be approved on those levels as well.**

**Outgoing**

The outgoing approvals are the approved transfers of the sender. By default, all transfers where the sender equals the transaction initiator are allowed, and all others are disallowed. The sender can optionally approve other addresses to transfer on their behalf. If you are familiar with other blockchain NFTs and tokens, this is the same as those approvals.

The collection can define a default outgoing approved transfers for each user. They can then be updated by the user as they desire.

**Incoming**

The incoming approvals are the approved transfers of the recipient. By default, all transfers where the recipient equals the transaction initiator are allowed, and all others are disallowed. The recipient can choose whether to block or allow incoming transfers via their incoming approved transfers. This is a new concept introduced by BitBadges

The collection can define a default incoming approved transfers for each user. They can then be updated by the user as they desire. They can then be updated by the user as they desire.

### Customization Options

At each level, we offer the following functionality for defining approved transfers. Mix and match any combinations

* Who can transfer to who? And who can initiate the transaction?
* When can the transfer take place?
* Which badges can be sent? For how long ([see ownership times](balances-transfers.md))? What amount?
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

### **Updatability**

We also allow any combination of the above functionality to be set to updatable or locked at specific times.

For example, the manager can permanently lock all combinations on a collection-level to make the transferability non-updatable. Or, they can lock it for a certain amount of time. Or, they can keep it updatable.

Once can even combine combinations, such as locking an approval for a specific badge for a specific time for specific users.

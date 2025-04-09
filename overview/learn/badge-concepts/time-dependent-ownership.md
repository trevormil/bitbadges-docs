# Time-Dependent Ownership

Typically, you may think of a balance in two parts: what you own and the amount you own (x10 of Badge IDs 1-199). BitBadges introduces a third part: ownership times.

For example, Bob owns x10 of Badge IDs 1-100 from January to March but x5 from March-December.

### Balance Components

1. **Badge IDs**: This refers to which badges in your possession.
2. **Badge Quantity / Amount**: This refers to how much of each badge you own.
3. **Ownership Times**: A time-based framework that governs the periods during which you possess specific badge quantities.

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Example of Ownership Structure

Consider Bob's ownership structure as an illustrative example:

Bob initially owns x10 of Badge IDs 1-100 from January to March. Subsequently, his ownership transitions to x5 of Badge IDs 1-100 from March to December.

### Advantages of the Ownership System

This ownership system brings forth several compelling benefits:

#### Subscription Tokens

By leveraging this system, the transfer of tokens designated for subscriptions becomes seamless. Auto-expiring tokens can be transferred without necessitating the revocation of permissions or initiating blockchain transactions.

#### Token Unlocks

Numerous projects feature stock or token unlock schedules that primarily rely on trust, rather than code-enforced mechanisms. This ownership framework empowers users to define and implement token unlock schedules natively, enhancing security and accountability.

#### Lending Mechanism

The ownership times concept facilitates badge lending for specified durations without the need for escrow services. Users can temporarily transfer their badges while retaining the ability to reclaim them after the designated timeframe.

### Usage Examples

To elucidate the functioning of this ownership system, let's delve into practical scenarios.

**Starting Balance**: Bob owns x10 of Badge IDs 1-100 from January to March.

**Example 1**: Bob's Transfer to Alice

* Bob transfers x10 of Badge IDs 1-100 from January to February to Alice.
* Result: Bob's ownership persists as x10 of Badge IDs 1-100 from February to March, while Alice becomes the owner of x10 of Badge IDs 1-100 from January to February.

**Example 2**: Bob's Partial Transfer to Alice

* Bob transfers x5 of Badge IDs 1-100 from January to March to Alice.
* Result: Both Bob and Alice now possess x5 of Badge IDs 1-100 during the January to March period.

**Example 3**: Complex Transfer Scenario

* Bob transfers x10 of Badge IDs 1-50 from January to February to Alice.
* Result: Bob retains ownership of x10 of Badge IDs 1-50 from February to March, and additionally, x10 of Badge IDs 50-100 from January to March. Meanwhile, Alice owns x10 of Badge IDs 1-50 from January to February.

Incorporating time-based ownership into the conventional badge ownership paradigm introduces enhanced flexibility, security, and functionality, enabling various usage scenarios that would otherwise be challenging to achieve.

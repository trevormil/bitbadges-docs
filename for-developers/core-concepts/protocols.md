# 🤖 Protocols

Protocols are a concept that allows you to treat certain collections from different users as a grouping intended for a specific purpose (the protocol's purpose). For example, the BitBadges Follow Protocol is a protocol that enables users to create "follow badge collections" and whoever owns the follow badge from that collection is considered followed.

This enables different collections to have different properties for the same protocol. For example, some users may want to host their follow badges off-chain, while some prefer decentralization on-chain. It also allows each respective user to access their own manager privileges for their collection, which wouldn't be possible all with one collection.

The protocol interface is a subset of the map interface explained on the prior page. It uses all the same Msgs, but it expects all values to be Uints and keys to be address-based for query purposes.

Over time, users can set their respective collections for protocols such as below. This is stored on-chain because reproducability and availability is important. All other custom protocol logic should be implemented separately.

```json
{
    "BitBadges Follow Protocol": 12, //collection ID 12 is used for my follows
    "Experiences Protocol": 13
}
```

**Genesis Conditions**

Protocols may have expected genesis conditions to be correctly implemented. There are no checks on-chain for genesis conditions when a user sets a protocol. This is to be handled on the app level by the implementation and handled accordingly.

Some examples:

* Creator must be manager at all times
* Total supply is locked at X for all IDs and times
* Balances must be stored on-chain

**Example Ideas**

* Messaging Protocol - Implement a protocol where each badge sent is a message from the sender to the recipient. The content can be attached to metadata or the **memo** field of the transaction.&#x20;
* Social Media Protocols - Implement protocols for posts, likes, follows, usernames, etc. Make use of the [alias addresses](../../overview/how-it-works/aliases.md) to allow a post to own a like badge, for example. Use the "No Balances Standard" for items where balances do not matter.

### Want to create a protocol?

See the map Msg definitions for handling the important stuff on-chain. Maps (and thus protocols) can also be fetched with the BitBadges API.

Other things that you may consider:

* If your protocol has genesis conditions in order to be implemented correctly, make it easy for apps to verify them, potentially with a library.
* Create an indexer for your protocol. For example, for a follow protocol, an important aspect of building an app is to query the followers / following. You can visit the BitBadges indexer to see how we index followers / following for the BitBadges Follow Protocol.

# 🤖 Protocols

Protocols are a concept that allows you to treat certain collections from different users as a grouping intended for a specific purpose (the protocol's purpose). For example, the BitBadges Follow Protocol is a protocol that enables users to create "follow badge collections" and whoever owns the follow badge from that collection is considered followed.

This enables different collections to have different properties for the same protocol. For example, some users may want to host their follow badges off-chain, while some prefer decentralization on-chain. It also allows each respective user to access their own manager privileges for their collection, which wouldn't be possible all with one collection.

The protocol interface is simple.

```typescript
/**
 * Protocol type
 *
 * @typedef {Object} Protocol
 *
 * @property {string} name - The name of the protocol.
 * @property {string} uri - The URI of the protocol.
 * @property {string} customData - The custom data of the protocol.
 * @property {string} createdBy - The cosmos address of the user who created the protocol.
 * @property {boolean} isFrozen - Whether the details fo the protocol are frozen or not.
 */
export interface Protocol {
  name: string;
  uri: string;
  customData: string;
  createdBy: string;
  isFrozen: boolean;
}
```

The uri and customData do not currently have a specific format. It is just a place to provide extra details.

Over time, users can set their respective collections for protocols such as below. This is stored on-chain because reproducability and availability is important. All other custom protocol logic should be implemented separately.

```json
{
    "BitBadges Follow Protocol": 12, //collection ID 12 is used for my follows
    "Experience Protocol": 13
}
```

**Genesis Conditions**

Protocols may have expected genesis conditions to be correctly implemented. There are no checks on-chain for genesis conditions when a user sets a protocol. This is to be handled on the app level by the implementation and handled accordingly.

Some examples:

* Creator must be manager at all times
* Total supply is locked at X for all IDs and times
* Balances must be stored on-chain

**Example Ideas**

* Experiences Protocol - Create a protocol which has a few badges for different experiences (trustworthy, scammer, met IRL, etc).  Each badge is handed out to others based on a user's experience with them.
* Messaging Protocol - Implement a protocol where each badge sent is a message from the sender to the recipient. The content can be attached to metadata or the **memo** field of the transaction.&#x20;
* Social Media Protocols - Implement protocols for posts, likes, follows, usernames, etc. Make use of the [alias addresses](../../overview/how-it-works/aliases.md) to allow a post to own a like badge, for example. Use the "No Balances Standard" for items where balances do not matter.

### Want to create a protocol?

See [MsgCreateProtocol](../create-and-broadcast-txs/cosmos-sdk-msgs/msgcreateprotocol.md) for creating a protocol on-chain. Other things that you may consider:

* Submit a pull request adding a template to the BitBadges frontend Create -> Template page which allows users to create a well-formatted collection and set it. Also, support the protocol in other applicable areas of the site.
* If your protocol has genesis conditions in order to be implemented correctly, make it easy for apps to verify them, potentially with a library.
* Create an indexer for your protocol. For example, for a follow protocol, an important aspect of building an app is to query the followers / following. You can visit the BitBadges indexer to see how we index followers / following for the BitBadges Follow Protocol.

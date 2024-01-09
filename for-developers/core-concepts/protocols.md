# 🤖 Protocols

Protocols are a concept that allows you to treat certain collections from different users as a grouping intended for a sepcific purpose (the protocol's purpose). For example, the BitBadges Follow Protocol is a protocol that enables users to create "follow badge collections" and whoever owns the folllow badge from that collection is considered followed.

This enables different collections to have different properties for the same protocol. For example, some users may want to host their follow badges off-chain, while some prefer decentralization on-chain. IT also allows each respective user to access their own manager privileges for their collection, which wouldn't be possible all with one collection.

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



Then, over time users can set their respective collections for protocols such as&#x20;

```json
{
    "BitBadges Follow Protocol": 12, //collection ID 12 is used for my follows
    "Experience Protocol": 13
}
```


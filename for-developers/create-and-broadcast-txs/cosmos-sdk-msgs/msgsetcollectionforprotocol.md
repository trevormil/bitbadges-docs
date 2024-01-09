# MsgSetCollectionForProtocol

MsgSetCollectionForProtocol sets the specific collection ID for the creator. For example, set my BitBadges Follow Protocol collection to collection ID 12.

If collectionId === 0, we use the last created collection. This can be leveraged in multi-msg transactions where you want to create a collection and set the ID to the just created collection. Typically, you do not know the collection ID until after the collection is created which is why this feature is useful. See below

```typescript
export interface MsgSetCollectionForProtocol<T extends NumberType> {
  creator: string,
  name: string,
  collectionId: T
}
```

**Combining with MsgCreateCollection or MsgUpdateCollection**

The two Msgs can be easily combined in the same transaction (as Cosmos transactions support multiple Msgs). See [here](../../bitbadges-sdk/common-snippets/creating-signing-and-broadcasting-txs.md) for an example of how to do it with the SDK. Just make sure MsgCreateCollection is executed first and precedes when it is actually used.

```typescript
const msgs: MessageGenerated[] = [];
msgs.push(...bootstrapCollections().map(x => createProtoMsg(x))) //MsgCreateCollections
msgs.push(...bootstrapSetProtocols().map(x => createProtoMsg(x))); //MsgSetCollectionForProtocol

//Note how MsgCreateAddressLists is first in the array
const txn = createTransactionPayload(txContext, msgs);
```

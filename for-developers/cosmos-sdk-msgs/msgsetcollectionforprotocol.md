# MsgSetValue

MsgSetValue sets the specific key-value pair for the mapId. For example, set my BitBadges Follow Protocol collection to collection ID 12.&#x20;

All values are stringified for comaptibility, but it will check they are in proper format within the Msg logic. You can set **options.useMostRecentCollectionId** to auto-fetch the latest collection ID. This can be leveraged in multi-msg transactions where you want to create a collection and set the ID to the just created collection. Typically, you do not know the collection ID until after the collection is created which is why this feature is useful. See below

```typescript
export interface iMsgSetValue {
  creator: string;
  mapId: string;
  key: string;
  value: string;
  options: iSetOptions;
}

export interface iSetOptions {
  useMostRecentCollectionId: boolean;
}
```

**Combining with MsgCreateCollection or MsgUpdateCollection**

The two Msgs can be easily combined in the same transaction (as Cosmos transactions support multiple Msgs). See [here](broken-reference) for an example of how to do it with the SDK. Just make sure MsgCreateCollection is executed first and precedes when it is actually used.

```typescript
const msgs: MessageGenerated[] = [];
msgs.push(...bootstrapCollections().map(x => createProtoMsg(x))) //MsgCreateCollections
msgs.push(...bootstrapSetProtocols().map(x => createProtoMsg(x))); //MsgSetCollectionForProtocol

//Note how MsgCreateAddressLists is first in the array
const txn = createTransactionPayload(txContext, msgs);
```

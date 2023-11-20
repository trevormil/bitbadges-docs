# MsgCreateAddressMappings

To create an on-chain AddressMapping, you can use the MsgCreateAddressMappings. See [here](../concepts/address-mappings-lists.md) to learn more about AddressMappings.

```typescript
export interface MsgCreateAddressMappings {
  creator: string;
  addressMappings: AddressMapping[];
}
```

```typescript
export interface AddressMapping {
  mappingId: string;

  addresses: string[];
  includeAddresses: boolean;

  uri: string; 
  customData: string;
}
```



**Combining with MsgCreateCollection or MsgUpdateCollection**

Oftentimes, you want to create a new AddressMapping, so it can be used in a MsgCreateCollection or MsgUpdateCollection (for example, defining transferability using a new, custom mapping).

Before an AddressMapping can be used, it needs to be defined on-chain. The two Msgs can be easily combined in the same transaction (as Cosmos transactions support multiple Msgs). See [here](../../sdk/common-snippets/creating-signing-and-broadcasting-txs.md) for an example of how to do it with the SDK. Just make sure MsgCreateAddressMappings is executed first and precedes when it is actually used.

```typescript
const msgs: MessageGenerated[] = [];
msgs.push(...bootstrapLists().map(x => createProtoMsg(x))); //MsgCreateAddressMappings
msgs.push(...bootstrapCollections().map(x => createProtoMsg(x))) //MsgCreateCollections

//Note how MsgCreateAddressMappings is first in the array
const txn = createTransactionPayload(txContext, msgs);
```


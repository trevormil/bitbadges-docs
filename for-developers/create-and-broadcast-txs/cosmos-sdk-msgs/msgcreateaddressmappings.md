# MsgCreateAddressLists

To create an on-chain AddressList, you can use the MsgCreateAddressLists. See [here](../../core-concepts/address-lists-lists.md) to learn more about AddressLists.

Reminder: on-chain address lists are immutable, permanent. and non-deletable.

```typescript
export interface MsgCreateAddressLists {
  creator: string;
  addressLists: AddressList[];
}
```

```typescript
export interface AddressList {
  listId: string;

  addresses: string[];
  whitelist: boolean;

  uri: string; 
  customData: string;
}
```



**Combining with MsgCreateCollection or MsgUpdateCollection**

Oftentimes, you want to create a new AddressList, so it can be used in a MsgCreateCollection or MsgUpdateCollection (for example, defining transferability using a new, custom list ID).

Before an AddressList can be used, it needs to be defined on-chain. The two Msgs can be easily combined in the same transaction (as Cosmos transactions support multiple Msgs). See [here](../../bitbadges-sdk/common-snippets/creating-signing-and-broadcasting-txs.md) for an example of how to do it with the SDK. Just make sure MsgCreateAddressLists is executed first and precedes when it is actually used.

```typescript
const msgs: MessageGenerated[] = [];
msgs.push(...bootstrapLists().map(x => createProtoMsg(x))); //MsgCreateAddressLists
msgs.push(...bootstrapCollections().map(x => createProtoMsg(x))) //MsgCreateCollections

//Note how MsgCreateAddressLists is first in the array
const txn = createTransactionPayload(txContext, msgs);
```


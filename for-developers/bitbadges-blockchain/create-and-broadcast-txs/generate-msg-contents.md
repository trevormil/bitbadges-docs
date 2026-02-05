# Generate Msg Contents

### **Building the Msgs**

You can build out the transaction from the exported Proto type definitions from our SDK. This allows you to create transactions with multiple Msg types in one tx. See [here](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/proto) for all proto Msg definitions.

```typescript
import { proto } from 'bitbadgesjs-sdk';

//proto.cosmos.module for standard Cosmos
//proto.tokenization for BitBadges x/tokenization
const MsgDeleteCollection = proto.tokenization.MsgDeleteCollection;
const MsgCreateCollection = proto.tokenization.MsgCreateCollection;
const MsgUpdateCollection = proto.tokenization.MsgUpdateCollection;
const MsgTransferTokens = proto.tokenization.MsgTransferTokens;

const protoMsgs = [
    new MsgDeleteCollection({ collectionId: '1', creator: 'bb...' }),
    //Add more here (executed in order)
];
```

### Building the Transaction

```typescript
import { TxContext, createTransactionPayload } from 'bitbadgesjs-sdk';

const txContext: TxContext = { ... } //See prior page

const txnPayload = createTransactionPayload(txContext, protoMsgs);
```

The outputted payload will be in the following format.

```typescript
export interface TransactionPayload {
    legacyAmino: {
        body: TxBody;
        authInfo: AuthInfo;
        signBytes: string;
    };
    signDirect: {
        body: TxBody;
        authInfo: AuthInfo;
        signBytes: string;
    };
    txnString: string;
    txnJson: Record<string, any>;
}
```

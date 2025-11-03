# Generate Msg Contents

### **Building the Msgs**

You can build out the transaction from the exported Proto type definitions from our SDK. This allows you to create transactions with multiple Msg types in one tx. See [here](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/proto) for all proto Msg definitions.

```typescript
import { proto } from 'bitbadgesjs-sdk';

//proto.cosmos.module for standard Cosmos
//proto.badges for BitBadges x/badges
const ProtoMsgDeleteCollection = proto.badges.MsgDeleteCollection;

const protoMsgs = [
    new ProtoMsgDeleteCollection({ collectionId: '1', creator: 'bb...' }),
    //Add more here (executed in order)
];
```

### Building the Transaction

<pre class="language-typescript"><code class="lang-typescript"><strong>import { TxContext, createTransactionPayload } from 'bitbadgesjs-sdk';
</strong><strong>
</strong><strong>const txContext: TxContext = { ... } //See prior page
</strong>
const txnPayload = createTransactionPayload(txContext, protoMsgs);
</code></pre>

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

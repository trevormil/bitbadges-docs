# Generate Msg Contents

The first step is to know what Msg type you are trying to submit and build out the contents of the Msgs in the transaction.&#x20;

If you plan to use [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast), the transaction building is already handled for you behind the scenes. The only applicable section below is Building the Msgs. This site is also a good reference if you want to explore the different types of messages.

### **Building the Msgs**

You can build out the transaction from the exported Proto type definitions. This allows you to create transactions with multiple Msg types in one tx. It also supports all Msgs for the BitBadges blockchain (even standard Cosmos SDK ones). Note that certain NumberTypes may need to be stringified before creating a proto object.

See [https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/proto](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/proto) for all proto Msg definitions.

```typescript
import { proto } from 'bitbadgesjs-sdk';

//proto.cosmos.module for standard Cosmos
//proto.badges for BitBadges x/badges
//proto.wasmx for BitBadges x/wasmx
//proto.protocols for BitBadges x/protocols
const ProtoMsgDeleteCollection = proto.badges.MsgDeleteCollection;

const protoMsgs = [
    new ProtoMsgDeleteCollection({ collectionId: '1', creator: 'bb...' }),
    //Add more here (executed in order)
];
```

### Building the Transaction

```typescript
const txContext = { ... } //See prior page

const txnPayload = createTransactionPayload(txContext, protoMsgs);
```

The outputted payload will be in the following format.

For Cosmos, you will use signDIrect.

For Ethereum/Solana/Bitcoin, you will use txnString.

If you stringify txnJSON and SHA256 it, you will get the content hash from the txnString.

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

### Next Steps

Once you have the generated transaction, you now need to determine how you want to sign and broadcast your transaction. You have two options:

1. Use [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast) - This is a visual UI that you can simply copy and paste your Msgs into. You can get the JSON string for each message with protoMsg.toJsonString(). Generating all additional transaction details, gas, fees, and signing is all outsourced to the user interface. This is the recommended option if you do not require programmatically submitting TXs. Navigate to [Sign + Broadcast - bitbadges.io](sign-+-broadcast-bitbadges.io.md) if this is your desired option.
2. Generate, sign, and broadcast directly to a running blockchain node. This is more technical and has more steps but can be done programmatically. Navigate to the corresponding Signing page if this is your desired option ([Signing - Cosmos](signing-cosmos.md), [Signing - Ethereum](signing-ethereum.md), [Signing - Solana](signing-solana.md), [Signing - Bitcoin](signing-bitcoin.md)).

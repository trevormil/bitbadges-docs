# Generate Msg Contents

### **Building the Msgs**

You can build out the transaction from the exported Proto type definitions from our SDK. This allows you to create transactions with multiple Msg types in one tx. See [here](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/src/proto) for all proto Msg definitions.

```typescript
import { proto } from 'bitbadgesjs-sdk';

//proto.cosmos.module for standard Cosmos
//proto.badges for BitBadges x/badges
const MsgDeleteCollection = proto.badges.MsgDeleteCollection;
const MsgCreateCollection = proto.badges.MsgCreateCollection;
const MsgUpdateCollection = proto.badges.MsgUpdateCollection;
const MsgTransferTokens = proto.badges.MsgTransferTokens;

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
    // Optional: EVM transaction details (present when evmAddress is provided in TxContext)
    evmTx?: {
        to: string;        // Precompile address (0x1001, 0x1002, or 0x1003)
        data: string;      // Encoded function call data
        value: string;     // Always "0" for precompiles
        functionName: string; // Function name for debugging
    };
}
```

**EVM Support:**

When you provide an `evmAddress` in the `TxContext`, the SDK automatically converts supported messages to EVM precompile calls. The `evmTx` field will be populated with the EVM transaction details. See [Signing - Ethereum](./signing-ethereum.md) for complete details.

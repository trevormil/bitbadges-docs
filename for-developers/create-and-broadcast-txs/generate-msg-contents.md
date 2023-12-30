# Generate Msg Contents

The first step is to know what Msg type you are trying to submit and build out the contents of the Msgs in the transaction.

See here for [sample Msgs](https://github.com/BitBadges/bitbadges-indexer/blob/master/src/setup). You can also visit [here](https://github.com/BitBadges/bitbadges-indexer/blob/master/src/bootstrap.ts) for how we bootstrap and simulate Msgs for a development network in plain JavaScript. See the frontend code for how we do so within a dApp. Both follow this tutorial pretty closely.

Note in the tutorials below, you do need a chain, sender, fee, and memo variable. However, if you plan to use  [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast), this is already handled for you behind the scenes. You can just enter dummy values for these variables if you plan to use  [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast). If you plan to broadcast programmatically, you will need to generate these yourself. If you haven't already see [Transaction Context](transaction-context.md).



You have two options when generating the contents of a transaction:

### **Option 1 - Single Msg**

If you are just submitting a single Msg and that Msg is either MsgSend or a BitBadges module Msg (other Cosmos Msgs are not supported), then you can use the provided helper functions from the SDK below.

```typescript
import { createTxMsgSend, createTxMsgDeleteCollection, ... } from 'bitbadgesjs-proto'

...

const txn = createTxMsgSend(chain, sender, fee, memo, params)
//const txn = createTxMsgDeleteCollection(chain, sender, fee, memo, params)
```

### **Option 2 - Build Dynamically**

Option 2 is you can build out the transaction from the Proto definitions. This allows you to create transactions with multiple Msg types in one tx. It also supports all Msgs for the BitBadges blockchain (even standard Cosmos SDK ones). Note that certain NumberTypes may need to be stringified before creating a proto object.

```typescript
import { createProtoMsg } from 'bitbadgesjs-proto'
import { MsgCreateAddressMappings as ProtoMsgCreateAddressMappings, MsgUpdateCollection as ProtoMsgUpdateCollection } from 'bitbadgesjs-proto/dist/proto/badges/tx_pb';
```

```typescript
const msg1 = new ProtoMsgCreateAddressMappings({
    creator: convertToCosmosAddress(ethWallet.address),
    addressMappings: [{
      ...
    }, {
      ...
    }]
  });
  
const msg2 = new ProtoMsgUpdateCollection({
  ...
});

const msgs = [msg1, msg2].map(x => createProtoMsg(x)));
const txn = createTransactionPayload({ chain, sender, memo: '', fee: { denom: 'badge', amount: '1', gas: '4000000' } }, msgs);
```



See [https://github.com/BitBadges/bitbadgesjs/tree/main/packages/proto/src/proto](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/proto/src/proto) for all proto definitions. They are not exported from the root, so you will have to find their absolute path (e.g. 'bitbadgesjs-proto/dist/proto/badges/tx\_pb');

### Output

The outputted **txn** will be in the following format. Each of these has a different purpose depending on which signature mode you plan to use (Solana/Bitcoin vs Ethereum EIP712 vs Standard Cosmos SDK).

```typescript
export interface TxPayload {
  signDirect: {
    body: Proto.Cosmos.Transactions.Tx.TxBody
    authInfo: Proto.Cosmos.Transactions.Tx.AuthInfo
    signBytes: string
  }
  legacyAmino: {
    body: Proto.Cosmos.Transactions.Tx.TxBody
    authInfo: Proto.Cosmos.Transactions.Tx.AuthInfo
    signBytes: string
  }
  eipToSign: EIP712ToSign | undefined
  jsonToSign: string | undefined
}
```

### Next Steps

Once you have the generated **txn,** you now need to determine how you want to sign and broadcast your transaction. You have two options:

1. Use  [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast) - This is a visual UI that you can simply copy and paste your txn into. Generating all additional transaction details, gas, fees, and signing is all outsourced to the user interface. This is the recommended option if you do not require programmatically submitting TXs. Navigate to [Sign + Broadcast - bitbadges.io](sign-+-broadcast-bitbadges.io.md) if this is your desired option.
2. Generate, sign, and broadcast directly to a running blockchain node. This is more technical and has more steps but can be done programmatically. Navigate to the corresponding Signing page if this is your desired option ([Signing - Cosmos](signing-cosmos.md), [Signing - Ethereum](signing-ethereum.md), [Signing - Solana](signing-solana.md), [Signing - Bitcoin](signing-bitcoin.md)).


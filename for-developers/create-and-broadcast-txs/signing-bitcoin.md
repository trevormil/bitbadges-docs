# Signing - Bitcoin

**Signing with Bitcoin - Phantom Wallet**

Using the **txn** variable obtained from previous steps. We use the **jsonToSign** field and sign it as a personal signMessage using phantom wallet.&#x20;

We then add the Web3ExtensionBitcoin extension to tell the blockchain this is a Bitcoin signature.&#x20;

**chain** and **sender** should be the same as the transaction context.

```typescript
const message = txn.jsonToSign;
const encodedMessage = new TextEncoder().encode(message);
const signedMessage = await solanaProvider.request({
  method: "signMessage",
  params: {
    message: encodedMessage,
    display: "utf8",
  },
});
sig = signedMessage.signature.toString('hex');


let txnExtension = signatureToWeb3ExtensionBitcoin(chain, sender, sig);

// Create the txRaw
let rawTx = createTxRawEIP712(
  txn.legacyAmino.body,
  txn.legacyAmino.authInfo,
  txnExtension,
);
```

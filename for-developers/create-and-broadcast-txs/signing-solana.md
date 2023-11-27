# Signing - Solana

**Signing with Solana - Phantom Wallet**

Using the **txn** variable obtained from previous steps. We use the **jsonToSign** field and sign it as a personal signMessage using phantom wallet.&#x20;

We then add the Web3ExtensionSolana extension to tell the blockchain this is a Solana signature.&#x20;

**chain** and **sender** should be the same as the transaction context. **address** should be the native Solana address (Base58 format).&#x20;

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


let txnExtension = signatureToWeb3ExtensionSolana(chain, sender, sig, address);

// Create the txRaw
let rawTx = createTxRawEIP712(
  txn.legacyAmino.body,
  txn.legacyAmino.authInfo,
  txnExtension,
);
```

# Signing - Bitcoin

```
⚠️ WARNING: This signing method is only intended for highly custom applications. For most use cases, we recommend only using Cosmos SDK-native wallets instead.
```

**Signing with Bitcoin - Phantom Wallet**

```typescript
const getProvider = () => {
  if ('phantom' in window) {
    const phantomWindow = window as any;
    const provider = phantomWindow.phantom?.bitcoin;
    if (provider?.isPhantom) {
      return provider;
    }

    window.open('https://phantom.app/', '_blank');
  }
};

function bytesToBase64(bytes: Uint8Array) {
  const binString = String.fromCodePoint(...bytes);
  return btoa(binString);
}

const signTxn = async (context: TxContext, payload: TransactionPayload, messages: any[], simulate: boolean) => {
    const bitcoinProvider = getProvider();

    let sig = '';
    if (!simulate) {
      const message = payload.txnString;
      const encodedMessage = new TextEncoder().encode(message);
      const signedMessage = await bitcoinProvider.signMessage(address, encodedMessage);

      const base64Sig = bytesToBase64(signedMessage.signature);
      sig = Buffer.from(base64Sig, 'base64').toString('hex');
    }

    const txBody = createTxBroadcastBody(context, messages, sig);
    return txBody;
};
```

# Signing - Solana

```
⚠️ WARNING: This signing method is only intended for highly custom applications. For most use cases, we recommend only using Cosmos SDK-native wallets instead.
```

**Signing with Solana - Phantom Wallet**

```typescript
const getProvider = () => {
    if ('phantom' in window) {
        const phantomWindow = window as any;
        const provider = phantomWindow.phantom?.solana;
        if (provider?.isPhantom) {
            return provider;
        }

        window.open('https://phantom.app/', '_blank');
    }
};

const signTxn = async (context: TxContext, payload: TransactionPayload, messages: any[], simulate: boolean) => {
   let sig = '';
  if (!simulate) {
    const encodedMessage = new TextEncoder().encode(payload.txnString);

    const signedMessage = await getProvider().request({
      method: 'signMessage',
      params: {
        message: encodedMessage,
        display: 'utf8'
      }
    });
    sig = signedMessage.signature.toString('hex');
  }

  //Note: We need to pass in the solAddress manually here
  const txBody = createTxBroadcastBody(context, messages, sig, address);
  return txBody;
};
```

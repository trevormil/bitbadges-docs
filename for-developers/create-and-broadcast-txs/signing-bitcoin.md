# Signing - Bitcoin

**Signing with Bitcoin - Phantom Wallet**

Using the payload / context obtained from previous steps. We use the **jsonToSign** field and sign it as a personal signMessage using phantom wallet.

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

const signTxn = async (context: ExternalTxContext, payload: TransactionPayload, messages: any[], simulate: boolean) => {
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

## Output

This will leave you with a variable which is to be submitted to a running blockchain node. See [Broadcast to a Node.](broadcast-to-a-node.md)

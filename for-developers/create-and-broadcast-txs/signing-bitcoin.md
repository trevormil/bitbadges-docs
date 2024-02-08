# Signing - Bitcoin

**Signing with Bitcoin - Phantom Wallet**

Using the payload / context obtained from previous steps. We use the **jsonToSign** field and sign it as a personal signMessage using phantom wallet.&#x20;

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

const signTxn = async (context: TxContext, payload: TransactionPayload, simulate: boolean) => {
  if (!account) throw new Error('Account not found.');

  let sig = '';
  if (!simulate) {
    const message = payload.jsonToSign;
    const encodedMessage = new TextEncoder().encode(message);
    const signedMessage = await bitcoinProvider.signMessage(
      address,
      encodedMessage
    );

    const base64Sig = bytesToBase64(signedMessage.signature);
    sig = Buffer.from(base64Sig, 'base64').toString('hex');
  }

  const txBody = createTxBroadcastBodyBitcoin(context, payload, sig);
  return txBody;
};
```



### Full Snippet

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/BitcoinContext.tsx" %}

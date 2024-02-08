# Signing - Solana

**Signing with Solana - Phantom Wallet**

Using the **txn** variable obtained from previous steps. We use the **jsonToSign** field and sign it as a personal signMessage using Phantom wallet.&#x20;

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

const signTxn = async (context: TxContext, payload: TransactionPayload, simulate: boolean) => {
  if (!account) throw new Error('Account not found.');

  let sig = '';
  if (!simulate) {
    const message = payload.jsonToSign;
    const encodedMessage = new TextEncoder().encode(message);
    const signedMessage = await solanaProvider.request({
      method: "signMessage",
      params: {
        message: encodedMessage,
        display: "utf8",
      },
    });
    sig = signedMessage.signature.toString('hex');
  }
  
  const solAddress = '....' // Solana Base58 address
  const txBody = createTxBroadcastBodySolana(context, payload, sig, solAddress);
  return txBody;
}
```



### Full Snippet

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/SolanaContext.tsx" %}

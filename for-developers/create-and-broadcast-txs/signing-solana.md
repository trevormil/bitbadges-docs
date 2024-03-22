# Signing - Solana

**Signing with Solana - Phantom Wallet**

Using the **txn** variable obtained from previous steps. We use the **jsonToSign** field and sign it as a personal signMessage using Phantom wallet.&#x20;

Phantom / Solana do not allow message signatures > \~1000 bytes. To workaround this, we allow signing the SHA256 hash of the JSON in cases where payload.jsonToSign.length > 1000.

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
    //Phantom has a weird error where messages must be < ~1000 bytes
    //If we are within limit, we can have user sign the JSON
    //Else, we hash the JSON and have user sign the hash

    const normalMessage = payload.jsonToSign.length < 1000;
    let message = payload.jsonToSign;
    let encodedMessage = new TextEncoder().encode(message);

    if (!normalMessage) {
      message = payload.jsonToSign;
      const sha256Message = CryptoJS.SHA256(message).toString();
      encodedMessage = new TextEncoder().encode(sha256Message);
    }

    const signedMessage = await getProvider().request({
      method: 'signMessage',
      params: {
        message: encodedMessage,
        display: 'utf8'
      }
    });
    sig = signedMessage.signature.toString('hex');
  }

  //We need to pass in solAddress manually here
  const txBody = createTxBroadcastBody(context, payload, sig, address);
  return txBody;
};

```



### Full Snippet

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/SolanaContext.tsx" %}

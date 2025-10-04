# Signing - Solana

Pre-Requisite: You have generated the transaction context, payload, and Msgs (see prior pages).

**Signing with Solana - Phantom Wallet**



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

const signTxn = async (context: ExternalTxContext, payload: TransactionPayload, messages: any[], simulate: boolean) => {
  if (!account) throw new Error('Account not found.');

  let sig = '';
  if (!simulate) {
    //Phantom has a weird error where messages must be < ~1000 bytes
    //If we are within limit, we can have user sign the JSON
    //Else, we hash the JSON and have user sign the hash
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

  //We need to pass in solAddress manually here
  const txBody = createTxBroadcastBody(context, messages, sig, address);
  return txBody;
};
```

## Output

This will leave you with a variable which is to be submitted to a running blockchain node. See [Broadcast to a Node.](broadcast-to-a-node.md)

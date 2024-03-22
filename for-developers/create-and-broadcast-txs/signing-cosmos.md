# Signing - Cosmos

Follow the prior pages to generate the context and payloads.

#### Signing with Keplr

```ts
const signTxn = async (context: TxContext, payload: TransactionPayload, simulate: boolean) => {
  if (!account) {
    throw new Error('Account does not exist');
  }
  const { sender } = context;
  await window.keplr?.enable(chainId);

  let signatures = [new Uint8Array(Buffer.from('0x', 'hex'))];
  if (!simulate) {
    const signResponse = await window?.keplr?.signDirect(
      chainId,
      sender.accountAddress,
      {
        bodyBytes: payload.signDirect.body.toBinary(),
        authInfoBytes: payload.signDirect.authInfo.toBinary(),
        chainId: chainId,
        accountNumber: new Long(sender.accountNumber)
      },
      {
        preferNoSetFee: true
      }
    );

    if (!signResponse) {
      throw new Error('No signature returned from Keplr');
    }

    signatures = [new Uint8Array(Buffer.from(signResponse.signature.signature, 'base64'))];
  }

  const hexSig = Buffer.from(signatures[0]).toString('hex');

  const txBody = createTxBroadcastBody(context, payload, hexSig);
  return txBody;
};

```

See [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/CosmosContext.tsx](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/CosmosContext.tsx) for full snippet. Or, the quickstart has it already implemted for you.

### Output

This will leave you with a variable which is to be submitted to a running blockchain node. See [Broadcast to a Node.](broadcast-to-a-node.md)

### Full Snippet

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/CosmosContext.tsx" %}




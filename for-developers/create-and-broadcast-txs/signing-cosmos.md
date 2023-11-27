# Signing - Cosmos

Pre-Requisite: You have generated the transaction and have it in the **txn** variable (see prior pages).

#### Signing with Keplr

```ts
// Follow the previous step to generate the msg object

const sender = {
  accountAddress: cosmosAddress,
  sequence: account.sequence ? Numberify(account.sequence) : 0,
  accountNumber: Numberify(account.accountNumber),
  pubkey: account.publicKey,
};
await window.keplr?.enable(chainId);

const signResponse = await window?.keplr?.signDirect(
  chainId,
  sender.accountAddress,
  {
    bodyBytes: txn.signDirect.body.serializeBinary(),
    authInfoBytes: txn.signDirect.authInfo.serializeBinary(),
    chainId: chainId,
    accountNumber: new Long(sender.accountNumber),
  },
  {
    preferNoSetFee: true,
  }
)

if (!signResponse) {
  return;
}

const signatures = [
  new Uint8Array(Buffer.from(signResponse.signature.signature, 'base64')),
]

const { signed } = signResponse;

const txRaw = createTxRaw(
  signed.bodyBytes,
  signed.authInfoBytes,
  signatures,
)
```

See [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/CosmosContext.tsx](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/CosmosContext.tsx) for full snippet.

### Output

This will leave you with a **txRaw** variable which is to be submitted to a running blockchain node. See [Broadcast to a Node.](broadcast-to-a-node.md)





###



###

####

####

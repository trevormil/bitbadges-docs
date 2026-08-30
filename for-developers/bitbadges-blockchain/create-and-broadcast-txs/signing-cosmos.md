# Signing - Cosmos (Manual)

> **Looking for a simpler approach?** Check out [BitBadgesSigningClient](./signing-client.md) for a streamlined, all-in-one signing solution that handles account info, gas estimation, signing, and broadcasting automatically.

This page covers manual Cosmos signing, which gives you fine-grained control over the transaction lifecycle.

#### Signing with Keplr

```ts
const signTxn = async (
    context: TxContext,
    payload: TransactionPayload,
    protoMsgs: any[],
    simulate: boolean
) => {
    const { sender } = context;
    await window.keplr?.enable(chainId);

    let signatures = [new Uint8Array(Buffer.from('0x', 'hex'))];
    if (!simulate) {
        const signResponse = await window?.keplr?.signDirect(
            chainId,
            sender.address,
            {
                bodyBytes: payload.signDirect.body.toBinary(),
                authInfoBytes: payload.signDirect.authInfo.toBinary(),
                chainId: chainId,
                // v34+ account numbers are 64-bit hash-derived values above
                // Number.MAX_SAFE_INTEGER — build the Long from the string,
                // never from a JS number (`new Long(n)` truncates to 32 bits).
                accountNumber: Long.fromString(String(sender.accountNumber), true),
            },
            {
                preferNoSetFee: true,
            }
        );

        if (!signResponse) {
            throw new Error('No signature returned from Keplr');
        }

        signatures = [
            new Uint8Array(
                Buffer.from(signResponse.signature.signature, 'base64')
            ),
        ];
    }

    const hexSig = Buffer.from(signatures[0]).toString('hex');

    const txBody = createTxBroadcastBody(context, protoMsgs, hexSig);
    return txBody;
};
```

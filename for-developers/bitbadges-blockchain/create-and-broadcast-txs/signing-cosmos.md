# Signing - Cosmos

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
                accountNumber: new Long(sender.accountNumber),
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

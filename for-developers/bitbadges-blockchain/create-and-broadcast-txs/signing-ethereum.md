# Signing - Ethereum

```
⚠️ WARNING: This signing method is only intended for highly custom applications. For most use cases, we recommend only using Cosmos SDK-native wallets instead.
```

You can simply sign the **payload.txnString** with a personal_sign.&#x20;

```typescript
// Example: wagmi setup with signMessageAsync

const signTxn = async (
    context: ExternalTxContext,
    payload: TransactionPayload,
    messages: any[],
    simulate: boolean
) => {
    const sig = simulate
        ? ''
        : await signMessageAsync({ message: payload.txnString });

    return createTxBroadcastBody(context, messages, sig);
};
```

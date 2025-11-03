# Signing - Ethereum

You can simply sign the **payload.txnString** with a personal\_sign.&#x20;

```typescript
// Example: wagmi setup with signMessageAsync

const signTxn = async (context: ExternalTxContext, payload: TransactionPayload, messages: any[], simulate: boolean) => {
  const sig = simulate ? '' : await signMessageAsync({  message: payload.txnString });
 
  return  createTxBroadcastBody(context, messages, sig);
};
```

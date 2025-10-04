# Signing - Ethereum

Pre-Requisite: You have generated the transaction context, payload, and Msgs (see prior pages).

You can simply sign the **payload.txnString** with a personal\_sign. below, we use wasgmi's signMessageAsync but you can replace this with any wallet's implementation of personal\_sign.

```typescript
const signTxn = async (context: ExternalTxContext, payload: TransactionPayload, messages: any[], simulate: boolean) => {
  const sig = simulate
    ? ''
    : await signMessageAsync({
        message: payload.txnString
      });
 
  const txBody = createTxBroadcastBody(context, messages, sig);
  return txBody;
};
```

## Output

This will leave you with a variable which is to be submitted to a running blockchain node. See [Broadcast to a Node.](broadcast-to-a-node.md)

# Broadcast to a Node

**Pre-Req:** You have the body variable with a valid signature (see prior pages).

### **Simulating**

A good practice to have is to simulate the transaction before you actually broadcast and update the **fee** from the transaction context with up to date values.&#x20;

To do this, you can use&#x20;

```
https://api.bitbadges.io/api/v0/simulate
```

**or**

```typescript
http://URL:1317/cosmos/tx/v1beta1/simulate
```

This will return the gas used on a dry run of the transaction and any errors if it finds any. You can leave all signature fields empty because simulations do not check any signatures.

Note this tutorial is slightly out of order for clarity, the simulation step should typically be done before the user signs, so they only have to sign the final Msg with the up to date gas.

Once simulated, replace the expected gas you want in the transaction context.

```typescript
export interface SimulateTxRouteSuccessResponse<T extends NumberType> {
    gas_info: {
        gas_used: string;
        gas_wanted: string;
    };
    result: {
        data: string;
        log: string;
        events: {
            type: string;
            attributes: {
                key: string;
                value: string;
                index: boolean;
            }[];
        }[];
        msg_responses: any[];
    };
}
```

### **Broadcasting**

You can replace the URL below with any valid BitBadges blockchain node.

```typescript
`http://URL:1317${generateEndpointBroadcast()}`
```

Or, you can use the BitBadges API to broadcast.

```typescript
https://api.bitbadges.io/api/v0/broadcast
```

<pre class="language-typescript"><code class="lang-typescript"><strong>await BitBadgesApi.broadcastTx(body);
</strong></code></pre>

This will give you a response immediately. You should then use the tx\_response.txhash to view it on an explorer, query the blockchain directly, see if it had errors, and so on.

```typescript
export interface BroadcastTxRouteSuccessResponse<T extends NumberType> {
    tx_response: {
        code: number;
        codespace: string;
        data: string;
        events: {
            type: string;
            attributes: {
                key: string;
                value: string;
                index: boolean;
            }[];
        }[];
        gas_wanted: string;
        gas_used: string;
        height: string;
        info: string;
        logs: {
            events: {
                type: string;
                attributes: {
                    key: string;
                    value: string;
                    index: boolean;
                }[];
            }[];
        }[];
        raw_log: string;
        timestamp: string;
        tx: object | null;
        txhash: string;
    };
}
```


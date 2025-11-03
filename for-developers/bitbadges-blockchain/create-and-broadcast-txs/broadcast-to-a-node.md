# Broadcast to a Node

### **Simulating**

A good practice to have is to simulate the transaction before you actually broadcast and update the **fee** from the transaction context with up to date values. You can leave all signature fields empty because simulations do not check any signatures. In our signing examples (previous pages), simply set simulate = true.

```typescript
// https://api.bitbadges.io/api/v0/simulate
const res = await BitBadgesApi.simulateTx(txBody); //Returned from signing steps
```

This will return the gas used and success statuses on a dry run of the transaction.

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
// https://api.bitbadges.io/api/v0/broadcast
const res = await BitBadgesApi.broadcastTx(txBody); //Returned from signing steps
```

The response code should be 0 for a successful transaction.

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

### Polling

Once you have the tx hash from above, you can poll a node until the transaction is confirmed like below. Note this is a blockchain REST API\_URL, not the BitBadges API. You can also view it on explorers.

Use [https://lcd.bitbadges.io](https://lcd.bitbadges.io) for the BitBadges maintained node.

```typescript
const txHash = res.data.tx_response.txhash;
const code = res.data.tx_response.code;
if (code !== undefined && code !== 0) {
    throw new Error(
        `Error broadcasting transaction: Code ${code}: ${JSON.stringify(
            res.data.tx_response,
            null,
            2
        )}`
    );
}

let fetched = false;
while (!fetched) {
    try {
        const res = await axios.get(
            `${process.env.API_URL}/cosmos/tx/v1beta1/txs/${txHash}`
        );
        fetched = true;

        return res;
    } catch (e) {
        // wait 1 sec
        console.log('Waiting 1 sec to fetch tx');
        await new Promise((resolve) => setTimeout(resolve, 1000));
    }
}

return res;
```

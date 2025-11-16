# MsgSwapExactAmountInWithIBCTransfer

Swaps an exact amount of tokens in for a minimum amount of tokens out, then automatically sends the output tokens to another chain via IBC transfer.

This message combines a swap operation with an IBC transfer, enabling seamless cross-chain swaps in a single transaction.

## Swap and Transfer Properties

When executing this message:

-   Exact input amount is specified
-   Minimum output amount provides slippage protection
-   Swap fee is deducted from the input
-   Output tokens are automatically sent via IBC to the specified receiver
-   All operations are atomic - if any step fails, the entire transaction is rolled back

## TypeScript Interface

```typescript
export interface iIBCTransferInfo<T extends NumberType> {
    sourceChannel: string;
    receiver: string;
    memo: string;
    timeoutTimestamp: T;
}

export interface iMsgSwapExactAmountInWithIBCTransfer<T extends NumberType> {
    sender: string;
    routes: iSwapAmountInRoute<T>[];
    tokenIn: iCosmosCoin<T>;
    tokenOutMinAmount: T;
    ibcTransferInfo: iIBCTransferInfo<T>;
}
```

## JSON Example

```json
{
    "sender": "bb1abc123...",
    "routes": [
        {
            "pool_id": "1",
            "token_out_denom": "uosmo"
        }
    ],
    "token_in": {
        "denom": "uatom",
        "amount": "1000000"
    },
    "token_out_min_amount": "5000000",
    "ibc_transfer_info": {
        "source_channel": "channel-0",
        "receiver": "cosmos1xyz789...",
        "memo": "Cross-chain swap",
        "timeout_timestamp": "1234567890000000000"
    }
}
```

## Multi-Hop Swaps

The `routes` field allows for multi-hop swaps through multiple pools. The swap will execute through each pool in sequence before the IBC transfer.

## Slippage Protection

The `token_out_min_amount` field ensures that the user receives at least the specified amount of output tokens, protecting against price slippage during the swap.

## IBC Transfer Details

The `ibc_transfer_info` field specifies:

-   **source_channel**: The IBC channel to send tokens through
-   **receiver**: The address on the destination chain to receive the tokens
-   **memo**: Optional memo to include with the IBC transfer
-   **timeout_timestamp**: The timestamp after which the IBC transfer will timeout

## Atomic Execution

This message executes atomically:

1. Swap tokens through the specified route(s)
2. Verify minimum output amount is met
3. Send output tokens via IBC transfer

If any step fails, the entire transaction is rolled back and no state changes are committed.

## Swap Fees

Each pool in the swap route charges a swap fee, which is deducted from the input amount before the swap is executed. The IBC transfer fee is also deducted from the output tokens.

## Use Cases

-   **Cross-chain DeFi**: Swap tokens on one chain and send to another in a single transaction
-   **Liquidity Routing**: Route liquidity through multiple chains efficiently
-   **Automated Trading**: Execute complex cross-chain trading strategies atomically

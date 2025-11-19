# MsgSwapExactAmountIn

Swaps an exact amount of tokens in for a minimum amount of tokens out.

This message allows users to swap a specific amount of input tokens for output tokens, with slippage protection through the minimum output amount.

## Swap Properties

When executing a swap:

-   Exact input amount is specified
-   Minimum output amount provides slippage protection
-   Swap fee is deducted from the input
-   Price impact is calculated based on pool liquidity

## Proto Definition

```protobuf
// ===================== MsgSwapExactAmountIn
message MsgSwapExactAmountIn {
  option (amino.name) = "gamm/SwapExactAmountIn";
  option (cosmos.msg.v1.signer) = "sender";

  string sender = 1 [ (gogoproto.moretags) = "yaml:\"sender\"" ];
  repeated poolmanager.v1beta1.SwapAmountInRoute routes = 2
      [ (gogoproto.nullable) = false ];
  cosmos.base.v1beta1.Coin token_in = 3 [
    (gogoproto.moretags) = "yaml:\"token_in\"",
    (gogoproto.nullable) = false
  ];
  string token_out_min_amount = 4 [

    (gogoproto.customtype) = "cosmossdk.io/math.Int",
    (gogoproto.moretags) = "yaml:\"token_out_min_amount\"",
    (gogoproto.nullable) = false
  ];
  repeated Affiliate affiliates = 5;
}

message MsgSwapExactAmountInResponse {
  string token_out_amount = 1 [

    (gogoproto.customtype) = "cosmossdk.io/math.Int",
    (gogoproto.moretags) = "yaml:\"token_out_amount\"",
    (gogoproto.nullable) = false
  ];
}
```

### Affiliate

```protobuf
message Affiliate {
  string basis_points_fee = 1;
  string address = 2;
}
```

The `affiliates` field allows you to specify fee recipients who will receive a portion of the swap output as an affiliate fee. Fees are specified in basis points (1 basis point = 0.01%, 100 basis points = 1%) and are calculated on the swap output amount.

### JSON Example

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
    "affiliates": [
        {
            "basis_points_fee": "10",
            "address": "bb1..."
        }
    ]
}
```

## Multi-Hop Swaps

The `routes` field allows for multi-hop swaps through multiple pools. The swap will execute through each pool in sequence.

## Slippage Protection

The `token_out_min_amount` field ensures that the user receives at least the specified amount of output tokens, protecting against price slippage.

## Swap Fees

Each pool in the swap route charges a swap fee, which is deducted from the input amount before the swap is executed.

## Affiliate Fees

The `affiliates` field allows you to specify fee recipients who will receive a portion of the swap output. Affiliate fees are:

-   **Optional**: If not specified, no affiliate fees are deducted
-   **Calculated on output**: Fees are calculated on the swap output amount
-   **Multiple affiliates**: You can specify multiple affiliates, each receiving their specified fee
-   **Basis points**: Fees are specified in basis points (1 basis point = 0.01%)

**Example**: If a swap outputs 1,000,000 tokens and you specify an affiliate with 10 basis points (0.1%):

-   Affiliate receives: 1,000,000 × 0.001 = 1,000 tokens
-   User receives: 1,000,000 - 1,000 = 999,000 tokens

# Estimating Swaps

The swap estimation endpoint allows you to estimate the output amount and required messages for a token swap before executing it. Routing includes both native swaps (our gamm module) and routing through Skip:Go across IBC chains like Osmosis.

## Endpoint

```
POST https://api.bitbadges.io/api/v0/api/{version}/swaps/estimate
```

## Request

```typescript
export interface iEstimateSwapPayload {
    /** The token in to swap. Format: "amount:X,denom:Y" */
    tokenIn: string;
    /** The token out denom to swap to. */
    tokenOutDenom: string;
}
```

## Response

```typescript
export interface iEstimateSwapSuccessResponse {
    success: boolean;
    estimate: {
        tokenOutAmount: string;
        tokenInAmount: string;
        skipGoMsgs: SkipGoMessage[];
        doesSwap: boolean;
        lowLiquidityWarning?: boolean;
    };
}
```

## Skip Go Compatibility

We attempt to mirror Skip:Go support as much as possible. And, we plan to eventually get fully integrated directly. However, there are a couple of minor differences and we do not claim full compatibility yet:

-   **No Affiliates**: Affiliate functionality is not supported
-   **Limited Skip Support**: Skip doesn't support our routing yet, so their API, engines, explorers, and client might not support the full feature set
-   **Message Format**: The `skipGoMsgs` returned attempt to follow the format from the [Skip API](https://docs.skip.build/go/api-reference/prod/fungible/post-v2fungiblemsgs)
-   **Cosmos Only**: This route will only recommend Cosmos swaps. For other chains outside of Cosmos liek ETH and SOL, these are not supported yet.

## Example

```typescript
const response = await BitBadgesApi.estimateSwap({
    tokenIn: 'amount:1000000,denom:ubadge',
    tokenOutDenom: 'ibc/...',
});

console.log(response.estimate.tokenOutAmount);
console.log(response.estimate.skipGoMsgs);

// Use the msgs to get user signatures and execute the swap
```

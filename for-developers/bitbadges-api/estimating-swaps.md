# Estimating Swaps

The swap estimation endpoint allows you to estimate the output amount and required messages for a token swap before executing it. Routing includes both native swaps (our gamm module) and routing through Skip:Go across IBC chains like Osmosis.

## Endpoint

```
POST https://api.bitbadges.io/api/v0/api/{version}/swaps/estimate
```

```typescript
export interface iEstimateSwapPayload {
  /** The token in to swap. Format: "amount:X,denom:Y" */
  tokenIn: string;
  /** Optional chain ID for the token in. Defaults to "bitbadges-1" if not provided. */
  tokenInChainId?: string;
  /** The token out denom to swap to. */
  tokenOutDenom: string;
  /** Optional chain ID for the token out. Defaults to "bitbadges-1" if not provided. */
  tokenOutChainId?: string;
  /**
   * Mapping of chain IDs to addresses. Must include "bitbadges-1" with a valid BitBadges address.
   * Other chain addresses will be generated automatically from the bitbadges-1 address.
   */
  chainIdsToAddresses: Record<string, string>;
  /**
   * Optional mapping of chain IDs to affiliate fee recipients.
   * Structure: { [chainId]: { affiliates: Array<{ address: string; basis_points_fee: string }> } }
   */
  chainIdsToAffiliates?: Record<string, { affiliates: Array<{ address: string; basis_points_fee: string }> }>;
  /** Slippage tolerance as a percentage (0-100). Can be a string or number. */
  slippageTolerancePercent: string | number;
  /** Forcefully recheck compliance and avoid cache (5 minutes) */
  forcefulRecheckCompliance?: boolean;
}

export interface iEstimateSwapSuccessResponse {
  success: boolean;
  estimate: {
    tokenOutAmount: string;
    tokenInAmount: string;
    skipGoMsgs: SkipGoMessage[];
    assetPath: {
      denom: string;
      chainId: string;
      how: 'genesis' | 'swap' | 'transfer';
    }[];
    doesSwap: boolean;
    lowLiquidityWarning?: boolean;
    complianceNotPassedWarning?: boolean;
    complianceErrorMessage?: string;
  };
}
```

## Skip Go Compatibility

We attempt to mirror Skip:Go support as much as possible. And, we plan to eventually get fully integrated directly. However, there are a couple of minor differences and we do not claim full compatibility yet:

* **Limited Skip Support**: Skip doesn't support our routing yet, so their API, engines, explorers, and client might not support the full feature set
* **Message Format**: The `skipGoMsgs` returned attempt to follow the format from the [Skip API](https://docs.skip.build/go/api-reference/prod/fungible/post-v2fungiblemsgs)
* **Cosmos Only**: This route will only recommend Cosmos swaps. For other chains outside of Cosmos liek ETH and SOL, these are not supported yet.

## Example

```typescript
const response = await BitBadgesApi.estimateSwap({
// ...
});

console.log(response.estimate.tokenOutAmount);
console.log(response.estimate.skipGoMsgs);

// Use the msgs to get user signatures and execute the swap
```

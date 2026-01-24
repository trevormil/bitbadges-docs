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
  /** Optional chain ID for the token in. Defaults to "bitbadges-1" if not provided. */
  tokenInChainId?: string;
  /** The token out denom to swap to. */
  tokenOutDenom: string;
  /** Optional chain ID for the token out. Defaults to "bitbadges-1" if not provided. */
  tokenOutChainId?: string;
  /**
   * Mapping of chain IDs to addresses.
   * Only supports "bitbadges-1" (bech32 bb prefixed address for Cosmos-based chains and "1" (EVM-based chains with a standard 0x address)
   *
   * We will generate any other chain addresses from these addresses.
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

```

## Response

```typescript
export interface iEstimateSwapSuccessResponse {
  /** Whether the swap estimation was successful. */
  success: boolean;
  /** Detailed estimation information for the swap. */
  estimate: {
    /** The estimated amount of tokens that will be received (token out). */
    tokenOutAmount: string;
    /** The amount of tokens being swapped in (token in). */
    tokenInAmount: string;
    /**
     * Messages for multi-chain routing.
     * Contains either a multi-chain message (for Cosmos chains) or an EVM transaction (for EVM chains).
     * These messages are used to execute the swap across different chains if needed.
     */
    skipGoMsgs: SkipGoMessage[];
    /**
     * The path the asset takes through different chains and operations to complete the swap.
     * Each step in the path indicates the denom, chain ID, and how the asset moves (genesis, swap, or transfer).
     */
    assetPath: {
      denom: string;
      chainId: string;
      how: 'genesis' | 'swap' | 'transfer';
    }[];
    /** Whether an actual swap operation occurs (true) or if it's just a transfer. */
    doesSwap: boolean;
    /** Warning flag indicating if the liquidity pool has low liquidity, which may affect swap execution. */
    lowLiquidityWarning?: boolean;
    /** Warning flag indicating if compliance checks did not pass for this swap. This means swap is likely to fail on BitBadges pool swap with compliance checks. */
    complianceNotPassedWarning?: boolean;
    /** Detailed error message if compliance checks failed. */
    complianceErrorMessage?: string;
    /** Estimated time in seconds for the swap to complete (if available). */
    estimatedTime?: number;
    /** Fallback asset if swap is not possible. */
    fallbackAsset?: { denom: string; chainId: string; };
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

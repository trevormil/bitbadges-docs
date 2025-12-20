# 🤹 Support Multiple Standards

In the case of trying to support multiple standards dynamically, consider an approach such as this one.&#x20;

1. Use an alias system. For our liquidity pools we use badgeslp:collectionId:denom format. The denom + collection ID corresponds to the `cosmosCoinWrapperPaths` element of the collection with that ID and denom matching the denom. This element provides details about the conversion rate amounts and display decimals.&#x20;
2. Create a wrapped function like below where for each coin, you handle accordingly with your alias system. If it matches the alias, you use our standard and `MsgTransferTokens`. If not, use standard x/bank or whatever standard.       &#x20;
3. Anywhere you want to support both dynamically, call the wrapper function instead.

Note that depending on the execution context, your module, contract, etc may not have "forceful" permissions and may be treated as a normal user who has to set its user-level approvals. Handle these accordingly to ensure that the transfers will pass compliance and transferability.

```go
func (k Keeper) SendCoinsToPoolWithWrapping(ctx sdk.Context, bankKeeper types.BankKeeper, from sdk.AccAddress, to sdk.AccAddress, coins sdk.Coins) error {
	for _, coin := range coins {
		if k.CheckIsWrappedDenom(ctx, coin.Denom) {
			// If denom is a BitBadges denom, use the BitBadges standard
			err := k.SendNativeTokensToAddress(ctx, from.String(), to.String(), coin.Denom, sdkmath.NewUintFromBigInt(coin.Amount.BigInt()))
			if err != nil {
				return err
			}
		} else {
			// If denom is not a BitBadges denom, send the coins normally with x/bank keeper
			err := bankKeeper.SendCoins(ctx, from, to, sdk.NewCoins(coin))
			if err != nil {
				return customhookstypes.WrapErr(&ctx, err, "failed to send coins to pool: %s", coin.Denom)
			}
		}
	}

	return nil
}
```

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadgeschain/blob/master/x/badges/keeper/pool_integration.go" %}

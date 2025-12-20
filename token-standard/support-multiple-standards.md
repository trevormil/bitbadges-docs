# 🤹 Support Multiple Standards

In the case of trying to support multiple standards dynamically, consider an approach such as this one.&#x20;

1. Use an alias system. For our liquidity pools we use badgeslp:collectionId:denom format. The denom + collection ID corresponds to the `cosmosCoinWrapperPaths` element of the collection with that ID and denom matching the denom.&#x20;
2. The path element provides details about the conversion rate amounts. Handle the conversion (int amount -> Balance\[] array).
3. Create a wrapped function like below where for each coin, you handle accordingly with your alias system. If it matches the alias, you use our standard and `MsgTransferTokens`. If not, use standard x/bank or whatever standard.       &#x20;
4. Anywhere you want to support both dynamically, call the wrapper function instead.

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

```go
// SendNativeTokensToAddress sends native badges tokens to an address
func (k Keeper) SendNativeTokensToAddress(ctx sdk.Context, recipientAddress string, toAddress string, denom string, amount sdkmath.Uint) error {
	collection, err := k.ParseCollectionFromDenom(ctx, denom)
	if err != nil {
		return err
	}

	balancesToTransfer, err := GetBalancesToTransfer(collection, denom, amount)
	if err != nil {
		return err
	}

	// Create and execute MsgTransferTokens to ensure proper event handling and validation
	badgesMsgServer := NewMsgServerImpl(k)

	// Handle any approvals that need to be set
	updateApprovalsMsg ::= ...	
	_, err = badgesMsgServer.UpdateUserApprovals(ctx, updateApprovalsMsg)
	if err != nil {
		return err
	}

	// Important: This only allows auto-scanned approvals since no prioritized approvals are set
	msg := &badgestypes.MsgTransferTokens{
		Creator:      recipientAddress,
		CollectionId: collection.CollectionId,
		Transfers: []*badgestypes.Transfer{
			{
				From:        recipientAddress,
				ToAddresses: []string{toAddress},
				Balances:    balancesToTransfer,
			},
		},
	}

	_, err = badgesMsgServer.TransferTokens(ctx, msg)
	return err
}
```

<pre class="language-go"><code class="lang-go"><strong>// GetCorrespondingPath finds the CosmosCoinWrapperPath for a given denom
</strong>func GetCorrespondingPath(collection *badgestypes.TokenCollection, denom string) (*badgestypes.CosmosCoinWrapperPath, error) {
	baseDenom, err := ParseDenomPath(denom)
	if err != nil {
		return nil, err
	}

	cosmosPaths := collection.CosmosCoinWrapperPaths
	for _, path := range cosmosPaths {
		if path.AllowOverrideWithAnyValidToken {
			panic("") //advanced case not shown here
		}

		if path.Denom == baseDenom {
			return path, nil
		}
	}

	return nil, fmt.Errorf("path not found for denom: %s", denom)
}

// GetBalancesToTransfer calculates the balances to transfer for a given denom and amount
func GetBalancesToTransfer(collection *badgestypes.TokenCollection, denom string, amount sdkmath.Uint) ([]*badgestypes.Balance, error) {
	path, err := GetCorrespondingPath(collection, denom)
	if err != nil {
		return nil, err
	}

	conversionRateBalances := badgestypes.DeepCopyBalances(path.Balances)
	for _, balance := range conversionRateBalances {
		balance.Amount = balance.Amount.Mul(amount)
	}

	return balancesToTransfer, nil
}

// CheckIsWrappedDenom checks if a denom is a wrapped badges denom
func (k Keeper) CheckIsWrappedDenom(ctx sdk.Context, denom string) bool {
	if !CheckStartsWithBadges(denom) {
		return false // Not an alias
	}

	collection, err := k.ParseCollectionFromDenom(ctx, denom)
	if err != nil {
		return false
	}

	path, err := GetCorrespondingPath(collection, denom)
	if err != nil {
		return false
	}

	if path.AllowCosmosWrapping {
		return false // Edge case: 
	}

	return true
}

// ParseCollectionFromDenom parses a collection from a badges denom
func (k Keeper) ParseCollectionFromDenom(ctx sdk.Context, denom string) (*badgestypes.TokenCollection, error) {
	collectionId, err := ParseDenomCollectionId(denom)
	if err != nil {
		return nil, err
	}

	collection, found := k.GetCollectionFromStore(ctx, sdkmath.NewUint(collectionId))
	if !found {
		return nil, customhookstypes.WrapErr(&#x26;ctx, badgestypes.ErrInvalidCollectionID, "collection %s not found",
			sdkmath.NewUint(collectionId).String())
	}

	return collection, nil
}

func CheckStartsWithBadges(denom string) bool {
	return strings.HasPrefix(denom, "badgeslp:")
}

// GetPartsFromDenom parses a badges denom into its parts
func GetPartsFromDenom(denom string) ([]string, error) {
	if !CheckStartsWithBadges(denom) {
		return nil, fmt.Errorf("invalid denom: %s", denom)
	}

	parts := strings.Split(denom, ":")
	if len(parts) != 3 {
		return nil, fmt.Errorf("invalid denom: %s", denom)
	}
	return parts, nil
}

// ParseDenomCollectionId extracts the collection ID from a badges denom
func ParseDenomCollectionId(denom string) (uint64, error) {
	parts, err := GetPartsFromDenom(denom)
	if err != nil {
		return 0, err
	}

	// this is equivalent to split(':')[1]
	return strconv.ParseUint(parts[1], 10, 64)
}

// ParseDenomPath extracts the path from a badges denom
func ParseDenomPath(denom string) (string, error) {
	parts, err := GetPartsFromDenom(denom)
	if err != nil {
		return "", err
	}
	// this is equivalent to split(':')[2]
	return parts[2], nil
}
</code></pre>

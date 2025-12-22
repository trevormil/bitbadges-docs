# 🤹 Supporting Multiple Standards

To support multiple token standards, we recommend using a SendManager module with **alias denom routing**. It automatically routes transactions to the appropriate handler based on denomination prefixes.

For complete implementation details, see the [source code](https://github.com/BitBadges/bitbadgeschain). We recommend simply copying / importing the implementation and using it directly if all you need is x/badges and x/bank.

It is intended to be a drop-in replacement for the BankKeeper interface. Simply replace anywhere you call k.bankKeeper.SendCoins() with k.sendManagerKeeper.SendCoinsWithAliasRouting() (or whatever other functions you need) and get support for multiple token standards at once via dynamic routing.

```go
type SendManagerKeeper interface {
	SendCoinWithAliasRouting(ctx sdk.Context, fromAddressAcc sdk.AccAddress, toAddressAcc sdk.AccAddress, coin *sdk.Coin) error
	SendCoinsWithAliasRouting(ctx sdk.Context, fromAddressAcc sdk.AccAddress, toAddressAcc sdk.AccAddress, coins sdk.Coins) error
	FundCommunityPoolWithAliasRouting(ctx sdk.Context, fromAddressAcc sdk.AccAddress, coins sdk.Coins) error
	SpendFromCommunityPoolWithAliasRouting(ctx sdk.Context, toAddressAcc sdk.AccAddress, coins sdk.Coins) error
	SendCoinsFromModuleToAccountWithAliasRouting(ctx sdk.Context, moduleName string, toAddressAcc sdk.AccAddress, coins sdk.Coins) error
	SendCoinsFromAccountToModuleWithAliasRouting(ctx sdk.Context, fromAddressAcc sdk.AccAddress, moduleName string, coins sdk.Coins) error
	GetBalanceWithAliasRouting(ctx sdk.Context, address sdk.AccAddress, denom string) (sdk.Coin, error)
}
```

### How It Works

#### Pattern Matching

SendManager uses **prefix-based pattern matching** to route denoms. It checks each registered prefix against the denom and routes to the first matching handler:

```go
// Example: denom = "badgeslp:1:utoken"
func (k Keeper) getRouterForDenom(denom string) (types.AliasDenomRouter, bool) {
    // Check registered prefixes in order (e.g., ["badgeslp:", "badges:"])
    for _, prefix := range k.registeredPrefixes {
        // Does "badgeslp:1:utoken" start with "badgeslp:"? Yes!
        if strings.HasPrefix(denom, prefix) {
            // Return the router registered for this prefix
            return k.prefixToRouter[prefix], true
        }
    }
    // No prefix matched - will use bank keeper for standard coins
    return nil, false
}
```

**Examples**:

* `badgeslp:1:utoken` → matches `badgeslp:` prefix → routes to x/badges
* `uatom` → no prefix match → routes to x/bank (standard Cosmos SDK coin)

#### Alias Denoms

BitBadges format: `badgeslp:COLLECTION_ID:denom` (e.g., `badgeslp:1:utoken`). The integer amount converts to `Balances[]` via collection's `cosmosCoinWrapperPaths` field (where the conversion rate is defined). Note that this process has no wrapping involved. The conversion is simply an alias for the full `Balances[]` field for compatibility with other environments.

#### Auto-Scan Mode

All sends through SendManager use **auto-scan mode** (no prioritized approvals). The underlying transfer is executed via `MsgTransferTokens`:

```go
// From SendNativeTokensViaAliasDenom
msg := &badgestypes.MsgTransferTokens{
    Creator:      recipientAddress,
    CollectionId: collection.CollectionId,
    Transfers: []*badgestypes.Transfer{
        {
            From:        recipientAddress,
            ToAddresses: []string{toAddress},
            Balances:    balancesToTransfer,
            // Note: No PrioritizedApprovals field - uses auto-scan mode
        },
    },
}
badgesMsgServer.TransferTokens(ctx, msg)
```

#### User-Level Approvals

SendManager does **not** manage user-level approvals automatically. If they need to be handled, you must set them as needed elsewhere before / after calling SendManager. Note: All x/badges transfers require approvals to be satisfied on the collection, sender, and recipient level, where applicable.

This may be especially important for module addresses or special non-user addresses. They automatically inherit the defaults for the collection.

```go
// Example: Sometimes, you may need both pre and post approval updates to make stuff work and clean up.
preUpdateApprovalsMsg := &badgestypes.MsgUpdateUserApprovals{ ... }
badgesMsgServer.UpdateUserApprovals(ctx, preUpdateApprovalsMsg)

sendManagerKeeper.SendCoinsWithAliasRouting(ctx, from, to, coins)

postUpdateApprovalsMsg := &badgestypes.MsgUpdateUserApprovals{ ... }
badgesMsgServer.UpdateUserApprovals(ctx, postUpdateApprovalsMsg)
```

**Example: Setting approvals before sending (from `FundCommunityPoolViaAliasDenom`)**

For example, the community pool address (depending on the defaults) may not accept incoming approvals by default which is needed to transfer tokens to it in the x/badges module.

```go
func (k Keeper) FundCommunityPoolViaAliasDenom(
    ctx sdk.Context,
    fromAddress string,
    toAddress string,
    denom string,
    amount sdkmath.Uint,
) error {
    collection, err := k.ParseCollectionFromDenom(ctx, denom)
    if err != nil {
        return err
    }

    // Set auto-approvals for recipient to accept incoming transfers
    err = k.SetAllAutoApprovalFlagsForAddress(ctx, collection, toAddress)
    if err != nil {
        return err
    }

    // Now safe to send - recipient has auto-approvals set
    return k.SendNativeTokensViaAliasDenom(ctx, fromAddress, toAddress, denom, amount)
}

// SetAllAutoApprovalFlagsForAddress sets all auto-approval flags for an address
func (k Keeper) SetAllAutoApprovalFlagsForAddress(
    ctx sdk.Context,
    collection *badgestypes.TokenCollection,
    address string,
) error {
    badgesMsgServer := NewMsgServerImpl(k)
    updateApprovalsMsg := &badgestypes.MsgUpdateUserApprovals{
        Creator:                               address,
        CollectionId:                          collection.CollectionId,
        UpdateAutoApproveAllIncomingTransfers: true,
        AutoApproveAllIncomingTransfers:       true,
        UpdateAutoApproveSelfInitiatedOutgoingTransfers: true,
        AutoApproveSelfInitiatedOutgoingTransfers:       true,
        UpdateAutoApproveSelfInitiatedIncomingTransfers: true,
        AutoApproveSelfInitiatedIncomingTransfers:       true,
    }
    _, err := badgesMsgServer.UpdateUserApprovals(ctx, updateApprovalsMsg)
    return err
}
```

#### Routing Flow

```go
func (k Keeper) SendCoinsWithAliasRouting(ctx sdk.Context, from, to sdk.AccAddress, coins sdk.Coins) error {
    for _, coin := range coins {
        router, found := k.getRouterForDenom(coin.Denom)
        if found {
            // Alias denom - use custom router
            amountUint := sdkmath.NewUintFromBigInt(coin.Amount.BigInt())
            return router.SendNativeTokensViaAliasDenom(ctx, from.String(), to.String(), coin.Denom, amountUint)
        }
        // Standard coin - use bank keeper
        return k.bankKeeper.SendCoins(ctx, from, to, sdk.NewCoins(coin))
    }
}
```

### Usage

```go
// Sending - works for both standard coins ("uatom") and alias denoms ("badgeslp:1:utoken")
err := sendManagerKeeper.SendCoinsWithAliasRouting(ctx, from, to, coins)

// Querying - automatically routes to correct handler
balance, err := sendManagerKeeper.GetBalanceWithAliasRouting(ctx, address, denom)
```

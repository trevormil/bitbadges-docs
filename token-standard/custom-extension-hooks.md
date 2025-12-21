# 🕵️ Custom Extension Hooks

Within our module, we attempt to make it easy to extend it if you are a chain developer  In your `app.go` , you can wire up custom code that is run at certain points:

```go
// Register custom approval criteria checkers (optional)
app.BadgesKeeper.RegisterCustomApprovalCriteriaChecker(func(approval *types.CollectionApproval) []approvalcriteria.ApprovalCriteriaChecker {
	// These will be called on every approval we check (all levels - collection, incoming, outgoing)
	// Feel free to use approval.customData for custom inputs
	return nil
})

// Register custom global transfer checkers (optional)
app.BadgesKeeper.RegisterCustomGlobalTransferChecker(func(ctx sdk.Context, from string, to string, initiatedBy string, collection *types.TokenCollection, transferBalances []*types.Balance, memo string) []badgesmodulekeeper.GlobalTransferChecker {
	// Add custom logic as needed here
	// These will be called before every transfer
	return nil
})

// These will be called every time we attempt to set the collection.
app.BadgesKeeper.RegisterCustomCollectionVerifier(...)
```

# 🕵️ Custom Extension Hooks

Within our module, we attempt to make it easy to extend with custom configurations if you are a chain developer. Oftentimes, you want to implement minor global invariants custom to your chain.

In your `app.go` , you can wire up custom code that is run at certain points no matter the collection. If you need to use the `ctx` field, you can. State is rolled back if anything fails.&#x20;

### Custom Approval Criteria Checkers

Custom approval criteria checkers add validation logic during approval processing. These are run along with checking our native approval criteria (every level - collection, incoming, outgoing).

#### Example: Require Specific Address

```go
type RequireSpecificAddressChecker struct {
	requiredAddress string
}

func (c *RequireSpecificAddressChecker) Name() string {
	return "RequireSpecificAddressChecker"
}

func (c *RequireSpecificAddressChecker) Check(
	ctx sdk.Context,
	approval *types.CollectionApproval,
	collection *types.TokenCollection,
	to string,
	from string,
	initiator string,
	// ... other params
) (detErrMsg string, err error) {
	if initiator != c.requiredAddress {
		return "initiator must be " + c.requiredAddress,
			fmt.Errorf("initiator address mismatch")
	}
	return "", nil
}

// Usage in app.go:
app.BadgesKeeper.RegisterCustomApprovalCriteriaChecker(func(approval *types.CollectionApproval) []approvalcriteria.ApprovalCriteriaChecker {
	if approval.ApprovalId == "special-approval" {
		return []approvalcriteria.ApprovalCriteriaChecker{
			NewRequireSpecificAddressChecker("bb1abc123..."),
		}
	}
	return nil
})
```

### Custom Global Transfer Checkers

Custom global transfer checkers run before `HandleTransfer()` and can reject transfers at a global level. Here, you have access to the balances for the transfer.&#x20;

#### Example: Require Specific Memo

```go
type RequireSpecificMemoChecker struct {
	requiredMemo string
}

func (c *RequireSpecificMemoChecker) Name() string {
	return "RequireSpecificMemoChecker"
}

func (c *RequireSpecificMemoChecker) Check(
	ctx sdk.Context,
	from string,
	to string,
	initiatedBy string,
	collection *types.TokenCollection,
	transferBalances []*types.Balance,
	memo string,
) (detErrMsg string, err error) {
	if memo != c.requiredMemo {
		return fmt.Sprintf("memo must be '%s'", c.requiredMemo),
			fmt.Errorf("memo mismatch")
	}
	return "", nil
}

// Usage in app.go:
app.BadgesKeeper.RegisterCustomGlobalTransferChecker(func(
	ctx sdk.Context,
	from string,
	to string,
	initiatedBy string,
	collection *types.TokenCollection,
	transferBalances []*types.Balance,
	memo string,
) []badgesmodulekeeper.GlobalTransferChecker {
	if collection.CollectionId.String() == "1" {
		return []badgesmodulekeeper.GlobalTransferChecker{
			NewRequireSpecificMemoChecker("approved-transfer"),
		}
	}
	return nil
})
```

### Custom Collection Verifiers

Custom collection verifiers run before collections are stored and can validate collection-level invariants.

#### Example: Require Specific Manager

```go
type RequireSpecificManagerVerifier struct {
	requiredManager string
}

func (v *RequireSpecificManagerVerifier) Name() string {
	return "RequireSpecificManagerVerifier"
}

func (v *RequireSpecificManagerVerifier) VerifyCollection(ctx sdk.Context, collection *types.TokenCollection) error {
	currentManager := collection.GetManager(ctx.BlockTime().Unix())

	if currentManager != v.requiredManager {
		return fmt.Errorf("collection manager must be %s, got %s",
			v.requiredManager, currentManager)
	}

	return nil
}

// Usage in app.go:
app.BadgesKeeper.RegisterCustomCollectionVerifier(
	NewRequireSpecificManagerVerifier("bb1..."),
)
```

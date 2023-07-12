# Distributing Badges

Pre-Reading: [Approved Transfers](../must-know-concepts/approved-transfers.md)



Once badges are created, they are sent to the "Mint" address initially. From there, they can be distributed **according to the collection's approved transfers and user incoming approvals.**&#x20;

Since we are looking to transfer from the "Mint" address, **it is important that we set the OverridesFromApprovedOutgoingTransfers flag to true** in the collection's approved transfers. If not, the transfer will always fail because the "Mint" address has no approvals set.&#x20;

Also, note that approved transfers are first-match-only, so it is best practice to place the "Mint" transfers at the start of the array to enforce that balances are always escrowed, and no approvals&#x20;



As a reminder, a transfer is approved if it is satisfied on all three levels (collection, sender's outgoing, and recipient's incoming). The collection approval can choose to forcefully override the user-level approvals. And by default, we approve incoming transfers where to equals initiatedBy and outgoing transfers where from equals initiatedBy.





Below are some examples. Be sure to check out the full interface documentation to see all options. You can mix and match options as well. Also, visit [Ecosystem](../../overview/ecosystem.md) for useful distribution tools.

**Example 1:** For manual distributions, you should include an approved transfer such as the following (customized to your needs). This approves all badges to be sent forcefully by the current manager from the mint address' balances to anyone at any time.&#x20;

```go
ToMappingId:                            "All",
FromMappingId:                          "Mint",
InitiatedByMappingId:                   "Manager",
TransferTimes:                          GetFullUintRanges(), //1-MAX
BadgeIds:                               GetFullUintRanges(), //1-MAX
OwnedTimes: 				GetFullUintRanges(), //1-MAX
AllowedCombinations: []*types.IsCollectionTransferAllowed{
	{
		IsAllowed: true,
	},
},
ApprovalDetails: []*types.ApprovalDetails{
	{
		ApprovalId:                 "mint-test",
		OverridesFromApprovedOutgoingTransfers: true,
		OverridesToApprovedIncomingTransfers:   true,
	},
}
```

Again, you can customize the parameters to your needs. Be sure to check out the interface to see all your options.



**Example 2:** Or, you can distribute via a claim-based process. The following will distribute x1 of Badge ID 1, 2, 3, or 4 depending on which Merkle path is provided by the claiming user (e.g. used for a whitelist or code-based claim).

```go
{
	ApprovalDetails: []*types.ApprovalDetails{{
		PredeterminedBalances: &types.PredeterminedBalances{
			OrderCalculationMethod:  &types.PredeterminedOrderCalculationMethod{ 
				UseMerkleChallengeLeafIndex: true 
			},
			IncrementedBalances: &types.IncrementedBalances{
				StartBalances: []*types.Balance{
					{
						BadgeIds: GetOneUintRange(),
						Amount:   sdkmath.NewUint(1),
						OwnedTimes: GetFullUintRanges(),
					},
				},
				IncrementBadgeIdsBy: sdkmath.NewUint(1),
				IncrementOwnedTimesBy: sdkmath.NewUint(0),
			},
		},
		Uri: "",
		MerkleChallenges: []*types.MerkleChallenge{
			{
				ChallengeId: "testchallenge",
				Root:                             hex.EncodeToString(rootHash),
				ExpectedProofLength:              sdk.NewUint(2),
				MaxOneUsePerLeaf:                 true,
				UseLeafIndexForTransferOrder: true,
			},
		},
		ApprovalId:            "testing232",
		OverridesFromApprovedOutgoingTransfers: true,
		OverridesToApprovedIncomingTransfers:   true,
	}},
	AllowedCombinations: []*types.IsCollectionTransferAllowed{{
		IsAllowed: true,
	}},
	TransferTimes:        GetFullUintRanges(),
	OwnedTimes: 	      GetFullUintRanges(),
	BadgeIds:             GetFullUintRanges(),
	FromMappingId:        "Mint",
	ToMappingId:          "All",
	InitiatedByMappingId: "All",
}
```

See [Creating Claims](../tutorials/merkle-trees-for-claims.md) for how to create these Merkle trees.



**Example 3:** You can also enforce ApprovalDetails.MustOwnBadges to something such as the following. This enforces that only users who own x1 of Badge IDs 1 of Collection ID 1 can transfer. For example, this could be used for restricting who is approved to only premium members of collections. You could also enforce users to own x0 of a badge to enforce they don't own a negative badge.&#x20;

```go
{
	{
		CollectionId: sdk.NewUint(1),
		AmountRange: &types.UintRange{
			Start: sdkmath.NewUint(1),
			End:   sdkmath.NewUint(1),
		},
		BadgeIds: GetOneUintRanges(),    //1-1
		OwnedTimes: GetFullUintRanges(), //1-MAX
	}
}
```



**Example 4:** Within ApprovalDetails, you can also set MaxNumTransfers and ApprovalAmounts on any of the following levels: overall, perTo, perFrom, and perInitiatedBy address. The following will restrict each address from only receiving one transfer max, each from address from only transferring x1000 of each badge, and overall limits to 1000 transfers max.

```go
MaxNumTransfers: &types.MaxNumTransfers{
	OverallMaxNumTransfers: sdkmath.NewUint(1000),
	PerToAddressMaxNumTransfers: Sdkmath.NewUint(1),
},
ApprovalAmounts: &types.ApprovalAmounts{
	PerFromAddressApprovalAmount: sdkmath.NewUint(1000),
},
```

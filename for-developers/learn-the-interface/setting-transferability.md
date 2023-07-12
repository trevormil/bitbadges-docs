# Setting Transferability

In a [previous tutorial](distributing-badges.md), we only discussed transferring badges from the "Mint" addresses. However, you may want badge owners to be able to transfer to other badge owners.&#x20;

Note that setting UserApprovedTransfers is equivalent to this without the overrides options.

Also, note that the same interface is used, so any feature you have previously seen (such as MustOwnBadges) can also be set and used here.



**Example 1: Fully Transferable**

All users can transfer to all other users (if their user approvals are satisfied).

```go
ToMappingId:                            "All",
FromMappingId:                          "All",
InitiatedByMappingId:                   "All",
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
		OverridesFromApprovedOutgoingTransfers: false,
		OverridesToApprovedIncomingTransfers:   false,
	},
}
```

**Example 2: Freezing a User's Ability to Transfer**

This freezes the address cosmos123abcxyz... from transferring any badge. Note that we take first-match only, so this approved transfer would have to come before the one in Example 1 to actually be matched to.

```go
ToMappingId:                            "All",
FromMappingId:                          "cosmos123abcxyz...",
InitiatedByMappingId:                   "All",
TransferTimes:                          GetFullUintRanges(), //1-MAX
BadgeIds:                               GetFullUintRanges(), //1-MAX
OwnedTimes: 				GetFullUintRanges(), //1-MAX
AllowedCombinations: []*types.IsCollectionTransferAllowed{
	{
		IsAllowed: false,
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

**Example 3: Enabling Revoking From a User**

This enables the manager to revoke from cosmos123abcxyz... at any time. Again, note that we take first-match only, so this approved transfer would have to come before the one in Example 1 to actually be matched to.

```go
ToMappingId:                            "All",
FromMappingId:                          "cosmos123abcxyz...",
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

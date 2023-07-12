# Locking Transferability

In the previous tutorial, we discussed how to set the transferability of a collection. However, you may want to lock it to ensure that it is always transferable, always non-transferable, etc. This is done via the CanUpdateCollectionApprovedTransfers permissions.

Note that updating user approved incoming / outgoing approved transfers permissions is the same interface as well.&#x20;



**Example 1:** By setting CanUpdateCollectionApprovedTransfers to this, this will permanently disallow all updates to the collection's approved transfers. So what it is currently is what it will be forever.

```go
CanUpdateCollectionApprovedTransfers: []*types.CollectionApprovedTransferPermission{
	{
		DefaultValues: &types.CollectionApprovedTransferDefaultValues{
			FromMappingId:        "All",
			ToMappingId:          "All",
			PermittedTimes:       []*types.UintRange{},
			ForbiddenTimes:       GetFullUintRanges(),
			TimelineTimes:        GetFullUintRanges(),
			InitiatedByMappingId: "All",
			BadgeIds:             GetFullUintRanges(),
			TransferTimes:        GetFullUintRanges(),
			OwnedTimes: 	      GetFullUintRanges(),
		},
		Combinations: []*types.CollectionApprovedTransferCombination{
			{},
		},
	},
}
```

**Example 2:** By setting CanUpdateCollectionApprovedTransfers to this, this will permanently disallow all updates to the collection's approved transfers for only badge ID 1.

```go
CanUpdateCollectionApprovedTransfers: []*types.CollectionApprovedTransferPermission{
	{
		DefaultValues: &types.CollectionApprovedTransferDefaultValues{
			FromMappingId:        "All",
			ToMappingId:          "All",
			PermittedTimes:       []*types.UintRange{},
			ForbiddenTimes:       GetFullUintRanges(),
			TimelineTimes:        GetFullUintRanges(),
			InitiatedByMappingId: "All",
			BadgeIds:             GetOneUintRanges(), //1-1
			TransferTimes:        GetFullUintRanges(),
			OwnedTimes: 	      GetFullUintRanges(),
		},
		Combinations: []*types.CollectionApprovedTransferCombination{
			{},
		},
	},
}
```

**Example 3:** By setting CanUpdateCollectionApprovedTransfers to this, this will permanently disallow all updates to the collection's approved transfers for only the address, cosmos123xyz...

```go
CanUpdateCollectionApprovedTransfers: []*types.CollectionApprovedTransferPermission{
	{
		DefaultValues: &types.CollectionApprovedTransferDefaultValues{
			FromMappingId:        "cosmos123xyz...",
			ToMappingId:          "All",
			PermittedTimes:       []*types.UintRange{},
			ForbiddenTimes:       GetFullUintRanges(),
			TimelineTimes:        GetFullUintRanges(),
			InitiatedByMappingId: "All",
			BadgeIds:             GetFullUintRanges(), 
			TransferTimes:        GetFullUintRanges(),
			OwnedTimes: 	      GetFullUintRanges(),
		},
		Combinations: []*types.CollectionApprovedTransferCombination{
			{},
		},
	},
}
```

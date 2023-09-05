# Setting Transferability

In a [previous tutorial](distributing-badges.md), we only discussed transferring badges from the "Mint" addresses. However, you may want badge owners to be able to transfer to other badge owners.&#x20;

Note that setting UserApprovedTransfers is the same process as this. Also, note that the same interface is used, so any feature you have previously seen (such as MustOwnBadges) can also be set and used here.



Again, make sure these are placed in the correct oreder to satisfy the first-match policy.



**Example 1: Fully Transferable**

All users can transfer to all other users (if their user approvals are satisfied).

<pre class="language-go"><code class="lang-go"><strong>{
</strong>    "fromMappingId": "AllWithoutMint",
    "toMappingId": "AllWithoutMint",
    "initiatedByMappingId": "AllWithoutMint",
    "badgeIds": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "ownershipTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "approvalDetails": [], //No additional restrictions
    "transferTimes": [
      {
        "start": "1",
        "end": "18446744073709551615"
      }
    ],
    "allowedCombinations": [
      {
        "isApproved": true,
        ...
      }
    ]
  }
</code></pre>

**Example 2: Freezing a User's Ability to Transfer**

This freezes the address cosmos123abcxyz... from transferring any badge. Note that we take first-match only, so this approved transfer would have to come before the one in Example 1 to actually be matched to.

```go
ToMappingId:                            "AllWithoutMint",
FromMappingId:                          "cosmos123abcxyz...",
InitiatedByMappingId:                   "AllWithoutMint",
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

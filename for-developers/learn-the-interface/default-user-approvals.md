# Default User Approvals

By default, on the user level, we only allow incoming transfers if to equals initiatedBy and outgoing transfers if from equals initiatedBy.

For initial distribution, this may get tricky because by default you can't send to another address without their approval (to does not equal initiatedBy). This is done to enforce opt-in only collections where you can only receive a badge if you are the one who initiates the transaction or gives the approval.

To solve this, within MsgUpdateCollection, you can specify a DefaultApprovedIncomingTransfersTimeline and DefaultApprovedOutgoingTransfersTimeline as seen below. This allows you to set the initial approvals for users at collection creation time, and they can choose to update it how they would like from there. Note that these defaults cannot be updated.

So for example, let's say you want to transfer x1 of Badge ID 1 from "Mint" initiated by you (the "Manager"). By default, this isn't allowed as previously explained. However, you can set a default such as the one below to allow it.&#x20;

```go
DefaultApprovedIncomingTransfersTimeline: []*types.UserApprovedIncomingTransferTimeline{{
	ApprovedIncomingTransfers: []*types.UserApprovedIncomingTransfer{
		{
			FromMappingId:        "Mint",
			InitiatedByMappingId: "Manager",
			TransferTimes:        GetFullUintRanges(),
			OwnedTimes: GetFullUintRanges(),
			BadgeIds:             GetFullUintRanges(),
			AllowedCombinations: []*types.IsUserIncomingTransferAllowed{
				{
					IsAllowed: true,
				},
			},
		},
	},
	TimelineTimes: GetFullUintRanges(),
}},
DefaultApprovedOutgoingTransfersTimeline: []*types.UserApprovedOutgoingTransferTimeline{},
```

This would then allow you to make the necessary transfers.

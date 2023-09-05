# Distributing Badges

Pre-Reading: [Approved Transfers](../concepts/approved-transfers.md)

Once badges are created, they are sent to the "Mint" address initially. From there, they can be distributed **according to the collection's approved transfers and user approvals.**&#x20;

Since we are looking to transfer from the "Mint" address, **it is important that we set the overridesFromApprovedOutgoingTransfers flag to true** in the collection's approved transfers. If not, the transfer will always fail because the "Mint" address has no approvals set.&#x20;

Also, note that approved transfers are first-match-only, so it is best practice to place the "Mint" transfers at the start. It is your responsibility to enforce that badges are escrowed properly in the "Mint" address by proper design/

As a reminder, a transfer is approved if it is satisfied on all three levels (collection, sender's outgoing, and recipient's incoming). The collection approval can choose to forcefully override the user-level approvals. And by default, we approve incoming transfers where to equals initiatedBy and outgoing transfers where from equals initiatedBy.



**First-Match**

As a reminder of the first-match policy used, let's consider a transfer:

* Transfer: `(bob, alice, bob, 10, 1-2, 10-100)`
* Approved Transfers:
  * `(bob, alice, bob, 10, [1-2], [1000-2000]) -> (true, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [10-50]) -> (true, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [51-100]) -> (false, approvalDetails)`
  * `(bob, alice, bob, 10, [1-2], [10-100]) -> (true, approvalDetails)`
    * This last tuple is practically ignored because of the first-match policy.

In this case, the transfer would be approved for `(bob, alice, bob, 10, [1-2], [10-50])`, provided it adheres to `approvalDetails`. However, it wouldn't be approved for `(bob, alice, bob, 10, [1-2], [51-100])` due to incompatible owned times.



**Complete Example**

For example, a collection approved transfers timeline may look like this. Look through and view the comments to see what each part of the interface does.

```json
"collectionApprovedTransfersTimeline": [
    {
      "collectionApprovedTransfers": [
        {
          //This first element is for a claim
          //Anyone can claim badges 1-100 from the "Mint" address in a first-come firs-serve manner
          //Badges start with ID 1 and increment by 1 after each claim
           
          //Who can send? Who can receive? Who can initiate the claim?
          "fromMappingId": "Mint",
          "toMappingId": "AllWithMint",
          "initiatedByMappingId": "AllWithMint",
          //When can the transfers take place?
          "transferTimes": [
            {
              "start": "1691978400000",
              "end": "1723514400000"
            }
          ],
          //When can the badges be owned?
          "ownershipTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ],
          "badgeIds": [
            {
              "start": "1",
              "end": "100"
            }
          ],
          //Transfers matching the details above will be approved 
          //if the approvalDetails are satisfied
          "allowedCombinations": [
            {
              "isApproved": true
              ... //no manipulations of the above values, treat as is
            }
          ],
          "approvalDetails": [
            {
              "approvalId": "ee63dfe690c801a168b3beb239b4676815c9653f637a998e48a7ad4082a0eb65",
              "uri": "",
              "customData": "",
              "mustOwnBadges": [], //
              "approvalAmounts": {
                ... 
              },
              "maxNumTransfers": {
                "overallMaxNumTransfers": "0", //0 means unlimited
                "perFromAddressMaxNumTransfers": "0",
                "perToAddressMaxNumTransfers": "0",
                //Each unique address can only claim max one times
                "perInitiatedByAddressMaxNumTransfers": "1"
              },
              //Each transfer is predetermined.
              //Badges start w/ ID 1 and increment by 1 after every claim
              "predeterminedBalances": {
                "manualBalances": [],
                "incrementedBalances": {
                  "startBalances": [
                    {
                      "amount": "1",
                      "badgeIds": [
                        {
                          "start": "1",
                          "end": "1"
                        }
                      ],
                      "ownershipTimes": [
                        {
                          "start": "1691978400000",
                          "end": "1723514400000"
                        }
                      ]
                    }
                  ],
                  "incrementBadgeIdsBy": "1",
                  "incrementOwnershipTimesBy": "0"
                },
                //To calculate how many increments, we use the number of claims processed
                "orderCalculationMethod": {
                  "useMerkleChallengeLeafIndex": false,
                  "useOverallNumTransfers": true,
                  "usePerFromAddressNumTransfers": false,
                  "usePerInitiatedByAddressNumTransfers": false,
                  "usePerToAddressNumTransfers": false
                }
              },
              "merkleChallenges": [],
              "requireToEqualsInitiatedBy": false,
              "requireFromEqualsInitiatedBy": false,
              "requireToDoesNotEqualInitiatedBy": false,
              "requireFromDoesNotEqualInitiatedBy": false,
              "overridesToApprovedIncomingTransfers": false,
              //IMPORTANT: Since we are transferring from the "Mint" address,
              //we need to override its approvals bc it will not have any
              "overridesFromApprovedOutgoingTransfers": true
            }
          ],
        },
        {
          //Note that since fromMappingId does include "Mint",
          //we will overlap with "Mint" transfers.
          //These will be first-matched to the element above and never handled here.
          
          
          //This is for defining revokes
          //The manager can revoke any badge at any time forcefully
          "fromMappingId": "AllWithMint",
          "toMappingId": "AllWithMint",
          "initiatedByMappingId": "Manager",
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
          "transferTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ],
          "approvalDetails": [
            {
              "approvalId": "Revoke",
              //We forcefully override the user's incoming and outgoing approvals
              "overridesFromApprovedOutgoingTransfers": true,
              "overridesToApprovedIncomingTransfers": true,
              ...
            }
          ],
          
          "allowedCombinations": [
            {
              "isApproved": true,
              ...
            }
          ]
        }
      ],
      //The above values correspond to all times / won't change in the future
      "timelineTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ]
    }
  ],
```





Below are some other examples. Be sure to check out the full interface documentation to see all options. You can mix and match options as well. Also, visit [Ecosystem](../../overview/ecosystem.md) for useful distribution tools.

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

See [Creating Claims](../tutorials/create-merkle-claim.md) for how to create these Merkle trees.



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

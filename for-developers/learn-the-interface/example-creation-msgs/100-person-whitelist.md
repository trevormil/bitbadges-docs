# 100 Person Whitelist

```json
{
  "creator": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
  "collectionId": "0",
  "defaultApprovedIncomingTransfersTimeline": [],
  "defaultApprovedOutgoingTransfersTimeline": [],
  "defaultUserPermissions": {
    "canUpdateApprovedIncomingTransfers": [],
    "canUpdateApprovedOutgoingTransfers": []
  },
  "badgesToCreate": [
    {
      "amount": "1",
      "badgeIds": [
        {
          "start": "1",
          "end": "100"
        }
      ],
      "ownershipTimes": [
        {
          "start": "1692039600000",
          "end": "1723575600000"
        }
      ]
    }
  ],
  "updateCollectionPermissions": true,
  "balancesType": "Standard",
  "collectionPermissions": {
    "canArchiveCollection": [],
    "canCreateMoreBadges": [],
    "canDeleteCollection": [],
    "canUpdateBadgeMetadata": [],
    "canUpdateCollectionApprovedTransfers": [],
    "canUpdateCollectionMetadata": [],
    "canUpdateContractAddress": [],
    "canUpdateCustomData": [],
    "canUpdateInheritedBalances": [],
    "canUpdateManager": [],
    "canUpdateOffChainBalancesMetadata": [],
    "canUpdateStandards": []
  },
  "updateManagerTimeline": true,
  "managerTimeline": [],
  "updateCollectionMetadataTimeline": true,
  "collectionMetadataTimeline": [
    {
      "timelineTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ],
      "collectionMetadata": {
        "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
        "customData": ""
      }
    }
  ],
  "updateBadgeMetadataTimeline": true,
  "badgeMetadataTimeline": [
    {
      "timelineTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ],
      "badgeMetadata": [
        {
          "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
          "badgeIds": [
            {
              "start": "1",
              "end": "10000000000000"
            }
          ],
          "customData": ""
        }
      ]
    }
  ],
  "updateOffChainBalancesMetadataTimeline": true,
  "offChainBalancesMetadataTimeline": [],
  "updateCustomDataTimeline": true,
  "customDataTimeline": [],
  "updateInheritedBalancesTimeline": true,
  "inheritedBalancesTimeline": [],
  "updateCollectionApprovedTransfersTimeline": true,
  "collectionApprovedTransfersTimeline": [
    {
      "collectionApprovedTransfers": [
        {
          "fromMappingId": "Mint",
          "toMappingId": "AllWithMint",
          "initiatedByMappingId": "AllWithMint",
          "transferTimes": [
            {
              "start": "1691953200000",
              "end": "1723575600000"
            }
          ],
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
          "allowedCombinations": [
            {
              "initiatedByMappingOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "fromMappingOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "toMappingOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "badgeIdsOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "ownershipTimesOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "transferTimesOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "isApproved": true
            }
          ],
          "approvalDetails": [
            {
              "approvalId": "1e37d516d5907808c3378b3078530e436153239f62e8d18318439fd3d9d57060",
              "uri": "",
              "customData": "",
              "mustOwnBadges": [],
              "approvalAmounts": {
                "overallApprovalAmount": "0",
                "perFromAddressApprovalAmount": "0",
                "perToAddressApprovalAmount": "0",
                "perInitiatedByAddressApprovalAmount": "0"
              },
              "maxNumTransfers": {
                "overallMaxNumTransfers": "0",
                "perFromAddressMaxNumTransfers": "0",
                "perToAddressMaxNumTransfers": "0",
                "perInitiatedByAddressMaxNumTransfers": "1"
              },
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
                          "start": "1692039600000",
                          "end": "1723575600000"
                        }
                      ]
                    }
                  ],
                  "incrementBadgeIdsBy": "1",
                  "incrementOwnershipTimesBy": "0"
                },
                "orderCalculationMethod": {
                  "useMerkleChallengeLeafIndex": false,
                  "useOverallNumTransfers": true,
                  "usePerFromAddressNumTransfers": false,
                  "usePerInitiatedByAddressNumTransfers": false,
                  "usePerToAddressNumTransfers": false
                }
              },
              "merkleChallenges": [
                {
                  "root": "a1546be0a44f78449ceec1b51cff344d2bff0ec9c22db82ef33321a8fa692cf8",
                  "expectedProofLength": "7",
                  "useCreatorAddressAsLeaf": true,
                  "useLeafIndexForTransferOrder": false,
                  "maxOneUsePerLeaf": false,
                  "uri": "ipfs://QmSCGwqBofFt69iGzKERw6z27Eszchd39AoPNLLGB18exE",
                  "customData": "",
                  "challengeId": "af6d24536e9f27a9706a96c7014d6b4948c399e842ff7519d2353b227873fc6a"
                }
              ],
              "requireToEqualsInitiatedBy": false,
              "requireFromEqualsInitiatedBy": false,
              "requireToDoesNotEqualInitiatedBy": false,
              "requireFromDoesNotEqualInitiatedBy": false,
              "overridesToApprovedIncomingTransfers": false,
              "overridesFromApprovedOutgoingTransfers": true
            }
          ],
        },
        {
          "fromMappingId": "AllWithoutMint",
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
          "toMapping": {
            "mappingId": "AllWithoutMint",
            "addresses": [
              "Mint"
            ],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "fromMapping": {
            "mappingId": "AllWithoutMint",
            "addresses": [
              "Mint"
            ],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "initiatedByMapping": {
            "mappingId": "AllWithoutMint",
            "addresses": [
              "Mint"
            ],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "approvalDetails": [],
          "transferTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ],
          "allowedCombinations": [
            {
              "isApproved": true,
              "toMappingOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "fromMappingOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "initiatedByMappingOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "badgeIdsOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "ownershipTimesOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              },
              "transferTimesOptions": {
                "invertDefault": false,
                "allValues": false,
                "noValues": false
              }
            }
          ]
        }
      ],
      "timelineTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ]
    }
  ],
  "updateStandardsTimeline": true,
  "standardsTimeline": [],
  "updateContractAddressTimeline": true,
  "contractAddressTimeline": [],
  "updateIsArchivedTimeline": true,
  "isArchivedTimeline": []
}
```

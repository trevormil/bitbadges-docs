# Claimable and Transferable

```
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
          "start": "1692018000000",
          "end": "1723554000000"
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
          "fromMapping": {
            "mappingId": "Mint",
            "addresses": [
              "Mint"
            ],
            "includeAddresses": true,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "toMappingId": "AllWithMint",
          "toMapping": {
            "mappingId": "AllWithMint",
            "addresses": [],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "initiatedByMappingId": "AllWithMint",
          "initiatedByMapping": {
            "mappingId": "AllWithMint",
            "addresses": [],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "transferTimes": [
            {
              "start": "1691931600000",
              "end": "1723554000000"
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
              "approvalId": "4c9c26ce741510c54aa61c8cf7ed49afdf78e4ff144e5d34a7596b33ab52e9a9",
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
                          "end": "100"
                        }
                      ],
                      "ownershipTimes": [
                        {
                          "start": "1692018000000",
                          "end": "1723554000000"
                        }
                      ]
                    }
                  ],
                  "incrementBadgeIdsBy": "0",
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
              "merkleChallenges": [],
              "requireToEqualsInitiatedBy": false,
              "requireFromEqualsInitiatedBy": false,
              "requireToDoesNotEqualInitiatedBy": false,
              "requireFromDoesNotEqualInitiatedBy": false,
              "overridesToApprovedIncomingTransfers": false,
              "overridesFromApprovedOutgoingTransfers": true
            }
          ],
          "balances": [
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
                  "start": "1692018000000",
                  "end": "1723554000000"
                }
              ]
            }
          ]
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

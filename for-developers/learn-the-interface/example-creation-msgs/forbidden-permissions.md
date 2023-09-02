# Forbidden Permissions

This shows a collection which forbids some permissions permanently.

```
{
  "creator": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
  "collectionId": "0",
  "defaultApprovedIncomingTransfersTimeline": [
    {
      "approvedIncomingTransfers": [
        {
          "fromMappingId": "AllWithMint",
          "fromMapping": {
            "mappingId": "AllWithMint",
            "addresses": [],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "initiatedByMapping": {
            "mappingId": "AllWithMint",
            "addresses": [],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "initiatedByMappingId": "AllWithMint",
          "transferTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ],
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
          "allowedCombinations": [
            {
              "isApproved": true,
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
          ],
          "approvalDetails": []
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
          "end": "10000"
        }
      ],
      "ownershipTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ]
    }
  ],
  "updateCollectionPermissions": true,
  "balancesType": "Standard",
  "collectionPermissions": {
    "canArchiveCollection": [
      {
        "defaultValues": {
          "timelineTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ],
          "permittedTimes": [],
          "forbiddenTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ]
        },
        "combinations": [
          {
            "permittedTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "forbiddenTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "timelineTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            }
          }
        ]
      }
    ],
    "canCreateMoreBadges": [
      {
        "defaultValues": {
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
          "permittedTimes": [],
          "forbiddenTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ]
        },
        "combinations": [
          {
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
            "permittedTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "forbiddenTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            }
          }
        ]
      }
    ],
    "canDeleteCollection": [
      {
        "defaultValues": {
          "permittedTimes": [],
          "forbiddenTimes": []
        },
        "combinations": [
          {
            "permittedTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "forbiddenTimesOptions": {
              "invertDefault": false,
              "allValues": true,
              "noValues": false
            }
          }
        ]
      }
    ],
    "canUpdateBadgeMetadata": [
      {
        "defaultValues": {
          "badgeIds": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ],
          "timelineTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ],
          "permittedTimes": [],
          "forbiddenTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ]
        },
        "combinations": [
          {
            "badgeIdsOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "permittedTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "forbiddenTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "timelineTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            }
          }
        ]
      }
    ],
    "canUpdateCollectionApprovedTransfers": [
      {
        "defaultValues": {
          "fromMappingId": "AllWithMint",
          "toMappingId": "AllWithMint",
          "initiatedByMappingId": "AllWithMint",
          "toMapping": {
            "mappingId": "AllWithMint",
            "addresses": [],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "fromMapping": {
            "mappingId": "AllWithMint",
            "addresses": [],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "initiatedByMapping": {
            "mappingId": "AllWithMint",
            "addresses": [],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "timelineTimes": [
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
          "permittedTimes": [],
          "forbiddenTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ]
        },
        "combinations": [
          {
            "initiatedByMappingOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
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
            "timelineTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "transferTimesOptions": {
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
            "permittedTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "forbiddenTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            }
          }
        ]
      },
      {
        "defaultValues": {
          "fromMappingId": "Mint",
          "toMappingId": "Mint",
          "initiatedByMappingId": "Mint",
          "toMapping": {
            "mappingId": "Mint",
            "addresses": [
              "Mint"
            ],
            "includeAddresses": true,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
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
          "initiatedByMapping": {
            "mappingId": "Mint",
            "addresses": [
              "Mint"
            ],
            "includeAddresses": true,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "timelineTimes": [
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
          "permittedTimes": [],
          "forbiddenTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ]
        },
        "combinations": [
          {
            "initiatedByMappingOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
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
            "timelineTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "transferTimesOptions": {
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
            "permittedTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "forbiddenTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            }
          }
        ]
      }
    ],
    "canUpdateCollectionMetadata": [],
    "canUpdateContractAddress": [],
    "canUpdateCustomData": [],
    "canUpdateInheritedBalances": [],
    "canUpdateManager": [
      {
        "defaultValues": {
          "timelineTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ],
          "permittedTimes": [],
          "forbiddenTimes": [
            {
              "start": "1",
              "end": "18446744073709551615"
            }
          ]
        },
        "combinations": [
          {
            "permittedTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "forbiddenTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            },
            "timelineTimesOptions": {
              "invertDefault": false,
              "allValues": false,
              "noValues": false
            }
          }
        ]
      }
    ],
    "canUpdateOffChainBalancesMetadata": [],
    "canUpdateStandards": []
  },
  "updateManagerTimeline": true,
  "managerTimeline": [
    {
      "manager": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
      "timelineTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ]
    }
  ],
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
          "initiatedByMappingId": "Manager",
          "initiatedByMapping": {
            "mappingId": "Manager",
            "addresses": [
              "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x"
            ],
            "includeAddresses": true,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
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
          "toMapping": {
            "mappingId": "AllWithMint",
            "addresses": [],
            "includeAddresses": false,
            "uri": "",
            "customData": "",
            "createdBy": ""
          },
          "transferTimes": [
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
          "badgeIds": [
            {
              "start": "1",
              "end": "18446744073709551615"
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
                "perInitiatedByAddressMaxNumTransfers": "0"
              },
              "predeterminedBalances": {
                "manualBalances": [],
                "incrementedBalances": {
                  "startBalances": [],
                  "incrementBadgeIdsBy": "0",
                  "incrementOwnershipTimesBy": "0"
                },
                "orderCalculationMethod": {
                  "useMerkleChallengeLeafIndex": false,
                  "useOverallNumTransfers": false,
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

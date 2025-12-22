# Collections

A collection is the primary entity that defines a group of related tokens with shared properties and rules. We refer you to other pages for more details on the different concepts that make up a collection.

Note: This is what is stored on-chain in storage for a collection. You may typically interact with similar concepts but moreso in Messages and Queries format.

## Proto Definition

```protobuf
message TokenCollection {
  // The unique identifier for this collection. This is assigned by the blockchain. First collection has ID 1.
  string collectionId = 1 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];

  // The metadata for the collection itself, which can vary over time.
  repeated CollectionMetadataTimeline collectionMetadataTimeline = 2;

  // The metadata for each token in the collection, also subject to changes over time.
  repeated TokenMetadataTimeline tokenMetadataTimeline = 3;

  // An arbitrary field that can store any data, subject to changes over time.
  repeated CustomDataTimeline customDataTimeline = 4;

  // The address of the manager of this collection, subject to changes over time.
  repeated ManagerTimeline managerTimeline = 5;

  // Permissions that define what the manager of the collection can do or not do.
  CollectionPermissions collectionPermissions = 6;

  // Transferability of the collection for collections with standard balances, subject to changes over time.
  // Overrides user approvals for a transfer if specified.
  // Transfer must satisfy both user and collection-level approvals.
  // Only applicable to on-chain balances.
  repeated CollectionApproval collectionApprovals = 7;

  // Standards that define how to interpret the fields of the collection, subject to changes over time.
  repeated StandardsTimeline standardsTimeline = 8;

  // Whether the collection is archived or not, subject to changes over time.
  // When archived, it becomes read-only, and no transactions can be processed until it is unarchived.
  repeated IsArchivedTimeline isArchivedTimeline = 9;

  // The default store of a balance for a user, upon genesis.
  UserBalanceStore defaultBalances = 10;

  // The user or entity who created the collection.
  string createdBy = 11;

  // The valid token IDs for this collection.
  repeated UintRange validTokenIds = 12;

  // The generated address of the collection. Also used to escrow Mint balances.
  string mintEscrowAddress = 13;

  // The IBC wrapper (sdk.coin) paths for the collection.
  repeated CosmosCoinWrapperPath cosmosCoinWrapperPaths = 14;

  // Collection-level invariants that cannot be broken.
  // These are set upon genesis and cannot be modified.
  CollectionInvariants invariants = 16;
}
```

### JSON

```json
{
    "collectionApprovals": [
        {
            "toListId": "All",
            "fromListId": "!Mint",
            "initiatedByListId": "!(bb1kx9532ujful8vgg2dht6k544ax4k9qzsp0sany)",
            "transferTimes": [
                {
                    "start": 1,
                    "end": "18446744073709551615"
                }
            ],
            "tokenIds": [
                {
                    "start": 1,
                    "end": "18446744073709551615"
                }
            ],
            "ownershipTimes": [
                {
                    "start": 1,
                    "end": "18446744073709551615"
                }
            ],
            "approvalId": "transferable-approval",
            "uri": "",
            "customData": "",
            "approvalCriteria": {
                "merkleChallenges": [],
                "mustOwnTokens": [],
                "predeterminedBalances": {
                    "manualBalances": [],
                    "incrementedBalances": {
                        "startBalances": [],
                        "incrementTokenIdsBy": 0,
                        "incrementOwnershipTimesBy": 0,
                        "durationFromTimestamp": 0,
                        "allowOverrideTimestamp": false,
                        "recurringOwnershipTimes": {
                            "startTime": 0,
                            "intervalLength": 0,
                            "chargePeriodLength": 0
                        },
                        "allowOverrideWithAnyValidToken": false
                    },
                    "orderCalculationMethod": {
                        "useOverallNumTransfers": false,
                        "usePerToAddressNumTransfers": false,
                        "usePerFromAddressNumTransfers": false,
                        "usePerInitiatedByAddressNumTransfers": false,
                        "useMerkleChallengeLeafIndex": false,
                        "challengeTrackerId": ""
                    }
                },
                "approvalAmounts": {
                    "overallApprovalAmount": 0,
                    "perToAddressApprovalAmount": 0,
                    "perFromAddressApprovalAmount": 0,
                    "perInitiatedByAddressApprovalAmount": 0,
                    "amountTrackerId": "e7444fe3e1a6fe74036965d4bf82e3c3bc50bed3bb38506a7331b48a426e620c",
                    "resetTimeIntervals": {
                        "startTime": 0,
                        "intervalLength": 0
                    }
                },
                "maxNumTransfers": {
                    "overallMaxNumTransfers": 0,
                    "perToAddressMaxNumTransfers": 0,
                    "perFromAddressMaxNumTransfers": 0,
                    "perInitiatedByAddressMaxNumTransfers": 0,
                    "amountTrackerId": "e7444fe3e1a6fe74036965d4bf82e3c3bc50bed3bb38506a7331b48a426e620c",
                    "resetTimeIntervals": {
                        "startTime": 0,
                        "intervalLength": 0
                    }
                },
                "autoDeletionOptions": {
                    "afterOneUse": false,
                    "afterOverallMaxNumTransfers": false,
                    "allowCounterpartyPurge": false,
                    "allowPurgeIfExpired": false
                },
                "requireToEqualsInitiatedBy": false,
                "requireFromEqualsInitiatedBy": false,
                "requireToDoesNotEqualInitiatedBy": false,
                "requireFromDoesNotEqualInitiatedBy": false,
                "overridesFromOutgoingApprovals": false,
                "overridesToIncomingApprovals": false,
                "coinTransfers": [],
                "userRoyalties": {
                    "percentage": 0,
                    "payoutAddress": ""
                },
                "dynamicStoreChallenges": [],
                "ethSignatureChallenges": [],
                "senderChecks": {
                    "mustBeWasmContract": false,
                    "mustNotBeWasmContract": false,
                    "mustBeLiquidityPool": false,
                    "mustNotBeLiquidityPool": false
                },
                "recipientChecks": {
                    "mustBeWasmContract": false,
                    "mustNotBeWasmContract": false,
                    "mustBeLiquidityPool": false,
                    "mustNotBeLiquidityPool": false
                },
                "initiatorChecks": {
                    "mustBeWasmContract": false,
                    "mustNotBeWasmContract": false,
                    "mustBeLiquidityPool": false,
                    "mustNotBeLiquidityPool": false
                },
                "altTimeChecks": null,
                "mustPrioritize": false
            },
            "version": 0
        }
    ],
    "collectionId": "73",
    "collectionMetadataTimeline": [
        {
            "collectionMetadata": {
                "uri": "ipfs://QmbidUFyrh4m9gCLihD8KXj9t8Y3rsiu5cmuikMjzQUQy2",
                "customData": ""
            },
            "timelineTimes": [
                {
                    "start": 1,
                    "end": "18446744073709551615"
                }
            ]
        }
    ],
    "collectionPermissions": {
        "canDeleteCollection": [],
        "canArchiveCollection": [
            {
                "timelineTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "permanentlyPermittedTimes": [],
                "permanentlyForbiddenTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ]
            }
        ],
        "canUpdateStandards": [],
        "canUpdateCustomData": [
            {
                "timelineTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "permanentlyPermittedTimes": [],
                "permanentlyForbiddenTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ]
            }
        ],
        "canUpdateManager": [],
        "canUpdateCollectionMetadata": [],
        "canUpdateValidTokenIds": [
            {
                "tokenIds": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "permanentlyPermittedTimes": [],
                "permanentlyForbiddenTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ]
            }
        ],
        "canUpdateTokenMetadata": [],
        "canUpdateCollectionApprovals": [
            {
                "fromListId": "All",
                "toListId": "All",
                "initiatedByListId": "All",
                "transferTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "tokenIds": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "ownershipTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "approvalId": "All",
                "permanentlyPermittedTimes": [],
                "permanentlyForbiddenTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ]
            },
            {
                "fromListId": "Mint",
                "toListId": "All",
                "initiatedByListId": "All",
                "transferTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "tokenIds": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "ownershipTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ],
                "approvalId": "All",
                "permanentlyPermittedTimes": [],
                "permanentlyForbiddenTimes": [
                    {
                        "start": 1,
                        "end": "18446744073709551615"
                    }
                ]
            }
        ]
    },
    "cosmosCoinWrapperPaths": [
        {
            "address": "bb1qatt2wn99umjn87eaz4ev965yut7vl8rpclxnvfejp9ww6s6fu4sdstzsn",
            "denom": "cubadge",
            "balances": [
                {
                    "amount": 1,
                    "tokenIds": [
                        {
                            "start": 1,
                            "end": 1
                        }
                    ],
                    "ownershipTimes": [
                        {
                            "start": 1,
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "symbol": "cubadge",
            "denomUnits": [
                {
                    "decimals": 9,
                    "symbol": "cBADGE",
                    "isDefaultDisplay": true
                }
            ],
            "allowOverrideWithAnyValidToken": false,
            "allowCosmosWrapping": false
        }
    ],
    "createdBy": "bb1w63npeee74ewuudzf8cgvy6at4jn4mjr0a9r5p",
    "customDataTimeline": [],
    "defaultBalances": {
        "balances": [],
        "incomingApprovals": [],
        "outgoingApprovals": [],
        "userPermissions": {
            "canUpdateOutgoingApprovals": [],
            "canUpdateIncomingApprovals": [],
            "canUpdateAutoApproveSelfInitiatedOutgoingTransfers": [],
            "canUpdateAutoApproveSelfInitiatedIncomingTransfers": [],
            "canUpdateAutoApproveAllIncomingTransfers": []
        },
        "autoApproveSelfInitiatedOutgoingTransfers": true,
        "autoApproveSelfInitiatedIncomingTransfers": true,
        "autoApproveAllIncomingTransfers": true
    },
    "invariants": {
        "noCustomOwnershipTimes": true,
        "maxSupplyPerId": 0,
        "cosmosCoinBackedPath": {
            "address": "bb18vjxx2hvr234rml4znk0ncnd6w583qjrxpg9xjg8hrhgc4kx7jcssqk6zr",
            "ibcDenom": "ubadge",
            "balances": [
                {
                    "amount": 1000000000,
                    "tokenIds": [
                        {
                            "start": 1,
                            "end": 1
                        }
                    ],
                    "ownershipTimes": [
                        {
                            "start": 1,
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "ibcAmount": 1000000000
        },
        "noForcefulPostMintTransfers": true,
        "disablePoolCreation": false
    },
    "isArchivedTimeline": [],
    "managerTimeline": [
        {
            "manager": "bb1w63npeee74ewuudzf8cgvy6at4jn4mjr0a9r5p",
            "timelineTimes": [
                {
                    "start": 1,
                    "end": "18446744073709551615"
                }
            ]
        }
    ],
    "mintEscrowAddress": "bb1jc62rquvfyyzyn807fdgj42hl9htz9dst0tlj7ewvcm9uky8ulwssaxyjj",
    "standardsTimeline": [
        {
            "standards": ["Liquidity Pools"],
            "timelineTimes": [
                {
                    "start": 1,
                    "end": "18446744073709551615"
                }
            ]
        }
    ],
    "tokenMetadataTimeline": [
        {
            "tokenMetadata": [
                {
                    "uri": "ipfs://QmSviTf2qQLpFU84oF8CrxNrdgXPdWwHEyrkX3k6j7iiqR",
                    "tokenIds": [
                        {
                            "start": 2,
                            "end": "18446744073709551615"
                        }
                    ],
                    "customData": ""
                },
                {
                    "uri": "ipfs://QmbidUFyrh4m9gCLihD8KXj9t8Y3rsiu5cmuikMjzQUQy2",
                    "tokenIds": [
                        {
                            "start": 1,
                            "end": 1
                        }
                    ],
                    "customData": ""
                }
            ],
            "timelineTimes": [
                {
                    "start": 1,
                    "end": "18446744073709551615"
                }
            ]
        }
    ],
    "validTokenIds": [
        {
            "start": 1,
            "end": 1
        }
    ]
}
```

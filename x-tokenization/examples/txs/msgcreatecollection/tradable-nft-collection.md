# Tradable Collection Example

This example demonstrates creating a tradable collection that supports orderbook-style trading.

## Transaction Structure

```json
[
    {
        "creator": "bb18el5ug46umcws58m445ql5scgg2n3tzagfecvl",
        "collectionId": "0",
        "defaultBalances": {
            "balances": [],
            "outgoingApprovals": [],
            "incomingApprovals": [],
            "autoApproveSelfInitiatedOutgoingTransfers": true,
            "autoApproveSelfInitiatedIncomingTransfers": true,
            "autoApproveAllIncomingTransfers": true,
            "userPermissions": {
                "canUpdateOutgoingApprovals": [],
                "canUpdateIncomingApprovals": [],
                "canUpdateAutoApproveSelfInitiatedOutgoingTransfers": [],
                "canUpdateAutoApproveSelfInitiatedIncomingTransfers": [],
                "canUpdateAutoApproveAllIncomingTransfers": []
            }
        },
        "validTokenIds": [
            {
                "start": "1",
                "end": "100"
            }
        ],
        "collectionPermissions": {
            "canDeleteCollection": [],
            "canArchiveCollection": [],
            "canUpdateStandards": [],
            "canUpdateCustomData": [],
            "canUpdateManager": [],
            "canUpdateCollectionMetadata": [],
            "canUpdateValidTokenIds": [],
            "canUpdateTokenMetadata": [],
            "canUpdateCollectionApprovals": [],
            "canAddMoreAliasPaths": [],
            "canAddMoreCosmosCoinWrapperPaths": []
        },
        "manager": "bb18el5ug46umcws58m445ql5scgg2n3tzagfecvl",
        "collectionMetadata": {
            "uri": "ipfs://QmdqD7VE4MTZz2V1XeCBqdFcQ9orE6a4PEUzbFi2SfFxoR",
            "customData": ""
        },
        "tokenMetadata": [
            {
                "uri": "ipfs://QmRbRYYyphz73apphqP3QQmkeZxbtMWmAxasGfhcw1RApD",
                "customData": "",
                "tokenIds": [
                    {
                        "start": "101",
                        "end": "18446744073709551615"
                    }
                ]
            },
            {
                "uri": "ipfs://QmdqD7VE4MTZz2V1XeCBqdFcQ9orE6a4PEUzbFi2SfFxoR",
                "customData": "",
                "tokenIds": [
                    {
                        "start": "1",
                        "end": "100"
                    }
                ]
            }
        ],
        "customData": "",
        "collectionApprovals": [
            {
                "fromListId": "Mint",
                "toListId": "All",
                "initiatedByListId": "bb18el5ug46umcws58m445ql5scgg2n3tzagfecvl",
                "transferTimes": [
                    {
                        "start": "1",
                        "end": "18446744073709551615"
                    }
                ],
                "tokenIds": [
                    {
                        "start": "1",
                        "end": "100"
                    }
                ],
                "ownershipTimes": [
                    {
                        "start": "1",
                        "end": "18446744073709551615"
                    }
                ],
                "uri": "",
                "customData": "",
                "approvalId": "a4ab9bc5e8752842a35a79238de4f627677ceae1d8fa9de44b52416e085f7f11",
                "approvalCriteria": {
                    "merkleChallenges": [],
                    "ethSignatureChallenges": [],
                    "predeterminedBalances": {
                        "manualBalances": [],
                        "incrementedBalances": {
                            "startBalances": [],
                            "incrementTokenIdsBy": "0",
                            "incrementOwnershipTimesBy": "0",
                            "durationFromTimestamp": "0",
                            "allowOverrideTimestamp": false,
                            "recurringOwnershipTimes": {
                                "startTime": "0",
                                "intervalLength": "0",
                                "chargePeriodLength": "0"
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
                        "overallApprovalAmount": "0",
                        "perToAddressApprovalAmount": "0",
                        "perFromAddressApprovalAmount": "0",
                        "perInitiatedByAddressApprovalAmount": "0",
                        "amountTrackerId": "a4ab9bc5e8752842a35a79238de4f627677ceae1d8fa9de44b52416e085f7f11",
                        "resetTimeIntervals": {
                            "startTime": "0",
                            "intervalLength": "0"
                        }
                    },
                    "maxNumTransfers": {
                        "overallMaxNumTransfers": "0",
                        "perToAddressMaxNumTransfers": "0",
                        "perFromAddressMaxNumTransfers": "0",
                        "perInitiatedByAddressMaxNumTransfers": "0",
                        "amountTrackerId": "d711e23dbe57b786dfb2d86d4a6792fb8c9951a18223065ea0c07d424225a738",
                        "resetTimeIntervals": {
                            "startTime": "0",
                            "intervalLength": "0"
                        }
                    },
                    "coinTransfers": [],
                    "requireToEqualsInitiatedBy": false,
                    "requireFromEqualsInitiatedBy": false,
                    "requireToDoesNotEqualInitiatedBy": false,
                    "requireFromDoesNotEqualInitiatedBy": false,
                    "overridesFromOutgoingApprovals": true,
                    "overridesToIncomingApprovals": true,
                    "autoDeletionOptions": {
                        "afterOneUse": false,
                        "afterOverallMaxNumTransfers": false
                    },
                    "userRoyalties": {
                        "percentage": "0",
                        "payoutAddress": ""
                    },
                    "mustOwnTokens": [],
                    "dynamicStoreChallenges": [],
                    "votingChallenges": [],
                    "evmQueryChallenges": [],
                    "senderChecks": {
                        "mustBeEvmContract": false,
                        "mustNotBeEvmContract": false,
                        "mustBeLiquidityPool": false,
                        "mustNotBeLiquidityPool": false
                    },
                    "recipientChecks": {
                        "mustBeEvmContract": false,
                        "mustNotBeEvmContract": false,
                        "mustBeLiquidityPool": false,
                        "mustNotBeLiquidityPool": false
                    },
                    "initiatorChecks": {
                        "mustBeEvmContract": false,
                        "mustNotBeEvmContract": false,
                        "mustBeLiquidityPool": false,
                        "mustNotBeLiquidityPool": false
                    },
                    "altTimeChecks": {
                        "offlineHours": [],
                        "offlineDays": []
                    },
                    "mustPrioritize": false
                },
                "version": "0"
            },
            {
                "fromListId": "!Mint",
                "toListId": "All",
                "initiatedByListId": "All",
                "transferTimes": [
                    {
                        "start": "1",
                        "end": "18446744073709551615"
                    }
                ],
                "tokenIds": [
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
                "uri": "",
                "customData": "",
                "approvalId": "transferable-approval",
                "approvalCriteria": {
                    "merkleChallenges": [],
                    "ethSignatureChallenges": [],
                    "predeterminedBalances": {
                        "manualBalances": [],
                        "incrementedBalances": {
                            "startBalances": [],
                            "incrementTokenIdsBy": "0",
                            "incrementOwnershipTimesBy": "0",
                            "durationFromTimestamp": "0",
                            "allowOverrideTimestamp": false,
                            "recurringOwnershipTimes": {
                                "startTime": "0",
                                "intervalLength": "0"
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
                        "overallApprovalAmount": "0",
                        "perToAddressApprovalAmount": "0",
                        "perFromAddressApprovalAmount": "0",
                        "perInitiatedByAddressApprovalAmount": "0",
                        "amountTrackerId": "d79af272f33e76e5ba77c4edc356ad5b2e4014dd93ec7cea2b45ba56c65e11ac",
                        "resetTimeIntervals": {
                            "startTime": "0",
                            "intervalLength": "0"
                        }
                    },
                    "maxNumTransfers": {
                        "overallMaxNumTransfers": "0",
                        "perToAddressMaxNumTransfers": "0",
                        "perFromAddressMaxNumTransfers": "0",
                        "perInitiatedByAddressMaxNumTransfers": "0",
                        "amountTrackerId": "d79af272f33e76e5ba77c4edc356ad5b2e4014dd93ec7cea2b45ba56c65e11ac",
                        "resetTimeIntervals": {
                            "startTime": "0",
                            "intervalLength": "0"
                        }
                    },
                    "coinTransfers": [],
                    "requireToEqualsInitiatedBy": false,
                    "requireFromEqualsInitiatedBy": false,
                    "requireToDoesNotEqualInitiatedBy": false,
                    "requireFromDoesNotEqualInitiatedBy": false,
                    "overridesFromOutgoingApprovals": false,
                    "overridesToIncomingApprovals": false,
                    "autoDeletionOptions": {
                        "afterOneUse": false,
                        "afterOverallMaxNumTransfers": false
                    },
                    "userRoyalties": {
                        "percentage": "0",
                        "payoutAddress": ""
                    },
                    "mustOwnTokens": [],
                    "dynamicStoreChallenges": [],
                    "votingChallenges": [],
                    "evmQueryChallenges": [],
                    "senderChecks": {
                        "mustBeEvmContract": false,
                        "mustNotBeEvmContract": false,
                        "mustBeLiquidityPool": false,
                        "mustNotBeLiquidityPool": false
                    },
                    "recipientChecks": {
                        "mustBeEvmContract": false,
                        "mustNotBeEvmContract": false,
                        "mustBeLiquidityPool": false,
                        "mustNotBeLiquidityPool": false
                    },
                    "initiatorChecks": {
                        "mustBeEvmContract": false,
                        "mustNotBeEvmContract": false,
                        "mustBeLiquidityPool": false,
                        "mustNotBeLiquidityPool": false
                    },
                    "altTimeChecks": {
                        "offlineHours": [],
                        "offlineDays": []
                    },
                    "mustPrioritize": false
                },
                "version": "0"
            }
        ],
        "standards": [
            "Tradable",
            "NFTs",
            "DefaultDisplayCurrency:ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349"
        ],
        "isArchived": false,
        "mintEscrowCoinsToTransfer": [],
        "cosmosCoinWrapperPathsToAdd": [],
        "aliasPathsToAdd": [],
        "invariants": {
            "noCustomOwnershipTimes": false,
            "maxSupplyPerId": "0",
            "cosmosCoinBackedPath": null,
            "noForcefulPostMintTransfers": false,
            "disablePoolCreation": false,
            "evmQueryChallenges": []
        }
    }
]
```

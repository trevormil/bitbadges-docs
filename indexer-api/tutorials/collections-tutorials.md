# Collections Tutorials

### Tutorial: Making a POST Request to `/api/v0/collection/batch`

#### Step 1: Understand the Route

The route `POST /api/v0/collection/batch` is used to fetch information about multiple collections in batch. It allows you to specify which details of the collections you want to fetch.

#### Step 2: Prepare Your Request

Create a JSON request body similar to the sample request provided. Here's what you need to include:

* `collectionsToFetch`: An array of objects, each representing a collection you want to fetch. For each collection:
  * `collectionId`: The ID of the collection you want to fetch.
  * `metadataToFetch`: An object specifying which metadata to fetch.&#x20;
    * You can set `doNotFetchCollectionMetadata` to `true` if you don't want to fetch collection metadata.
    *   You can also fetch specific metadata using the following fields

        ```typescript
        metadataIds?: NumberType[] | UintRange<NumberType>[];
        uris?: string[];
        badgeIds?: NumberType[] | UintRange<NumberType>[];
        ```
  *   `viewsToFetch`: An array of objects specifying which additional views of the collection to fetch.



      ```typescript
      {
          viewKey: 'latestActivity' | 'latestAnnouncements' | 'latestReviews' | 'owners' | 'merkleChallenges' | 'approvalsTrackers';
          bookmark: string; //bookmark returned by previous request
      }[];
      ```
  * `fetchTotalAndMintBalances`: Set to `true` if you want to fetch total and mint balances.
  * `handleAllAndAppendDefaults`: Set to `true` if you want to handle all details and append defaults. This will append empty, default values for all timeline times, and also, it will append the unhandled values for approved transfers (disallowed if unhandled on collection level, allowed for user outgoing approvals if unhandled but from === initiatedBy, etc).

#### Step 3: Send the Request

Using your preferred method (e.g., using a programming language like JavaScript with libraries like `axios`, `fetch`, or API testing tools like Postman), send a POST request to the endpoint `/api/v0/collection/batch` with the JSON request body you prepared.

#### Step 4: Handle the Response

The response will contain an array of collections with the requested details. Each collection object will include various attributes and timelines with their corresponding data.

**Step 5: Paginations / Fetching More**

Within the previous response, you will have received a **bookmark** for each view under the **views** field. To fetch the next page, you will need to specify the previously received bookmark for the last page in Step 2 for viewsToFetch.



**IMPORTANT: Note that it only fetches what is requested in the query options. It is your responsibility to cache and append to your previous responses.**&#x20;

See the updateCollection function [here](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/CollectionsContext.tsx) for reference.



#### Example Response

```json
{
    "collections": [
        {
            "_id": "1",
            "collectionId": "1",
            "managerTimeline": [
                {
                    "manager": "",
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "createdBlock": "1192",
            "defaultUserApprovedIncomingTransfersTimeline": [
                {
                    "approvedIncomingTransfers": [],
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "defaultUserApprovedOutgoingTransfersTimeline": [
                {
                    "approvedOutgoingTransfers": [],
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "defaultUserPermissions": {
                "canUpdateApprovedOutgoingTransfers": [],
                "canUpdateApprovedIncomingTransfers": []
            },
            "createdBy": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
            "balancesType": "Standard",
            "collectionApprovedTransfersTimeline": [
                {
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ],
                    "collectionApprovedTransfers": [
                        {
                            "fromMapping": {
                                "mappingId": "AllWithMint",
                                "addresses": [],
                                "includeAddresses": false,
                                "uri": "",
                                "customData": "",
                                "createdBy": ""
                            },
                            "fromMappingId": "AllWithMint",
                            "toMapping": {
                                "mappingId": "AllWithMint",
                                "addresses": [],
                                "includeAddresses": false,
                                "uri": "",
                                "customData": "",
                                "createdBy": ""
                            },
                            "toMappingId": "AllWithMint",
                            "initiatedByMapping": {
                                "mappingId": "AllWithMint",
                                "addresses": [],
                                "includeAddresses": false,
                                "uri": "",
                                "customData": "",
                                "createdBy": ""
                            },
                            "initiatedByMappingId": "AllWithMint",
                            "badgeIds": [
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
                            "ownershipTimes": [
                                {
                                    "start": "1",
                                    "end": "18446744073709551615"
                                }
                            ],
                            "approvalDetails": [],
                            "allowedCombinations": [
                                {
                                    "isApproved": false,
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
                                    "initiatedByMappingOptions": {
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
                                    }
                                }
                            ]
                        }
                    ]
                }
            ],
            "collectionMetadataTimeline": [
                {
                    "collectionMetadata": {
                        "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
                        "customData": ""
                    },
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "badgeMetadataTimeline": [
                {
                    "badgeMetadata": [
                        {
                            "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
                            "customData": "",
                            "badgeIds": [
                                {
                                    "start": "1",
                                    "end": "10000000000000"
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
            "offChainBalancesMetadataTimeline": [
                {
                    "offChainBalancesMetadata": {
                        "uri": "",
                        "customData": ""
                    },
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "customDataTimeline": [
                {
                    "customData": "",
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "inheritedBalancesTimeline": [
                {
                    "inheritedBalances": [],
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "standardsTimeline": [
                {
                    "standards": [],
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "contractAddressTimeline": [
                {
                    "contractAddress": "",
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "isArchivedTimeline": [
                {
                    "isArchived": false,
                    "timelineTimes": [
                        {
                            "start": "1",
                            "end": "18446744073709551615"
                        }
                    ]
                }
            ],
            "collectionPermissions": {
                "canDeleteCollection": [],
                "canArchiveCollection": [],
                "canUpdateContractAddress": [],
                "canUpdateOffChainBalancesMetadata": [],
                "canUpdateStandards": [],
                "canUpdateCustomData": [],
                "canUpdateManager": [],
                "canUpdateCollectionMetadata": [],
                "canCreateMoreBadges": [],
                "canUpdateBadgeMetadata": [],
                "canUpdateInheritedBalances": [],
                "canUpdateCollectionApprovedTransfers": []
            },
            "activity": [],
            "announcements": [],
            "reviews": [],
            "owners": [
                {
                    "_id": "1:Mint",
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
                                    "start": "1691794800000",
                                    "end": "1723330800000"
                                }
                            ]
                        }
                    ],
                    "cosmosAddress": "Mint",
                    "collectionId": "1",
                    "onChain": true,
                    "approvedOutgoingTransfersTimeline": [
                        {
                            "timelineTimes": [
                                {
                                    "start": "1",
                                    "end": "18446744073709551615"
                                }
                            ],
                            "approvedOutgoingTransfers": [
                                {
                                    "toMapping": {
                                        "mappingId": "AllWithMint",
                                        "addresses": [],
                                        "includeAddresses": false,
                                        "uri": "",
                                        "customData": "",
                                        "createdBy": ""
                                    },
                                    "toMappingId": "AllWithMint",
                                    "initiatedByMapping": {
                                        "mappingId": "AllWithMint",
                                        "addresses": [],
                                        "includeAddresses": false,
                                        "uri": "",
                                        "customData": "",
                                        "createdBy": ""
                                    },
                                    "initiatedByMappingId": "AllWithMint",
                                    "badgeIds": [
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
                                    "ownershipTimes": [
                                        {
                                            "start": "1",
                                            "end": "18446744073709551615"
                                        }
                                    ],
                                    "approvalDetails": [],
                                    "allowedCombinations": [
                                        {
                                            "isApproved": false,
                                            "toMappingOptions": {
                                                "invertDefault": false,
                                                "allValues": false,
                                                "noValues": false
                                            },
                                            "initiatedByMappingOptions": {
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
                                            }
                                        }
                                    ]
                                }
                            ]
                        }
                    ],
                    "approvedIncomingTransfersTimeline": [
                        {
                            "timelineTimes": [
                                {
                                    "start": "1",
                                    "end": "18446744073709551615"
                                }
                            ],
                            "approvedIncomingTransfers": [
                                {
                                    "fromMapping": {
                                        "mappingId": "AllWithMint",
                                        "addresses": [],
                                        "includeAddresses": false,
                                        "uri": "",
                                        "customData": "",
                                        "createdBy": ""
                                    },
                                    "fromMappingId": "AllWithMint",
                                    "initiatedByMapping": {
                                        "mappingId": "AllWithMint",
                                        "addresses": [],
                                        "includeAddresses": false,
                                        "uri": "",
                                        "customData": "",
                                        "createdBy": ""
                                    },
                                    "initiatedByMappingId": "AllWithMint",
                                    "badgeIds": [
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
                                    "ownershipTimes": [
                                        {
                                            "start": "1",
                                            "end": "18446744073709551615"
                                        }
                                    ],
                                    "approvalDetails": [],
                                    "allowedCombinations": [
                                        {
                                            "isApproved": false,
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
                                            }
                                        }
                                    ]
                                }
                            ]
                        }
                    ],
                    "userPermissions": {
                        "canUpdateApprovedIncomingTransfers": [],
                        "canUpdateApprovedOutgoingTransfers": []
                    }
                },
                {
                    "_id": "1:Total",
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
                                    "start": "1691794800000",
                                    "end": "1723330800000"
                                }
                            ]
                        }
                    ],
                    "cosmosAddress": "Total",
                    "collectionId": "1",
                    "onChain": true,
                    "approvedOutgoingTransfersTimeline": [
                        {
                            "timelineTimes": [
                                {
                                    "start": "1",
                                    "end": "18446744073709551615"
                                }
                            ],
                            "approvedOutgoingTransfers": [
                                {
                                    "toMapping": {
                                        "mappingId": "AllWithMint",
                                        "addresses": [],
                                        "includeAddresses": false,
                                        "uri": "",
                                        "customData": "",
                                        "createdBy": ""
                                    },
                                    "toMappingId": "AllWithMint",
                                    "initiatedByMapping": {
                                        "mappingId": "AllWithMint",
                                        "addresses": [],
                                        "includeAddresses": false,
                                        "uri": "",
                                        "customData": "",
                                        "createdBy": ""
                                    },
                                    "initiatedByMappingId": "AllWithMint",
                                    "badgeIds": [
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
                                    "ownershipTimes": [
                                        {
                                            "start": "1",
                                            "end": "18446744073709551615"
                                        }
                                    ],
                                    "approvalDetails": [],
                                    "allowedCombinations": [
                                        {
                                            "isApproved": false,
                                            "toMappingOptions": {
                                                "invertDefault": false,
                                                "allValues": false,
                                                "noValues": false
                                            },
                                            "initiatedByMappingOptions": {
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
                                            }
                                        }
                                    ]
                                }
                            ]
                        }
                    ],
                    "approvedIncomingTransfersTimeline": [
                        {
                            "timelineTimes": [
                                {
                                    "start": "1",
                                    "end": "18446744073709551615"
                                }
                            ],
                            "approvedIncomingTransfers": [
                                {
                                    "fromMapping": {
                                        "mappingId": "AllWithMint",
                                        "addresses": [],
                                        "includeAddresses": false,
                                        "uri": "",
                                        "customData": "",
                                        "createdBy": ""
                                    },
                                    "fromMappingId": "AllWithMint",
                                    "initiatedByMapping": {
                                        "mappingId": "AllWithMint",
                                        "addresses": [],
                                        "includeAddresses": false,
                                        "uri": "",
                                        "customData": "",
                                        "createdBy": ""
                                    },
                                    "initiatedByMappingId": "AllWithMint",
                                    "badgeIds": [
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
                                    "ownershipTimes": [
                                        {
                                            "start": "1",
                                            "end": "18446744073709551615"
                                        }
                                    ],
                                    "approvalDetails": [],
                                    "allowedCombinations": [
                                        {
                                            "isApproved": false,
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
                                            }
                                        }
                                    ]
                                }
                            ]
                        }
                    ],
                    "userPermissions": {
                        "canUpdateApprovedIncomingTransfers": [],
                        "canUpdateApprovedOutgoingTransfers": []
                    }
                }
            ],
            "merkleChallenges": [],
            "approvalsTrackers": [],
            "cachedBadgeMetadata": [
                {
                    "metadata": {
                        "_isUpdating": false,
                        "name": "Placeholder",
                        "description": "",
                        "image": "https://png.pngtree.com/element_pic/00/16/07/18578cd65e6ecaa.jpg"
                    },
                    "badgeIds": [
                        {
                            "start": "1",
                            "end": "10000000000000"
                        }
                    ],
                    "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
                    "metadataId": "1"
                }
            ],
            "managerInfo": {
                "_id": "",
                "cosmosAddress": "",
                "address": "",
                "chain": "Unknown",
                "publicKey": "",
                "accountNumber": "-1",
                "collected": [],
                "activity": [],
                "announcements": [],
                "reviews": [],
                "merkleChallenges": [],
                "approvalsTrackers": [],
                "seenActivity": "0",
                "createdAt": "0",
                "views": {}
            },
            "views": {
                "latestReviews": {
                    "ids": [],
                    "type": "Review",
                    "pagination": {
                        "bookmark": "nil",
                        "hasMore": false
                    }
                }
            }
        }
    ]
}
```


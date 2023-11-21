# Fetching Collections

### Tutorial: Making a POST Request to `/api/v0/collection/batch`

**Step 1: Understand the Route** The POST route `/api/v0/collection/batch` is utilized to retrieve information about multiple collections collectively. It empowers you to define which aspects of the collections you intend to retrieve.

```typescript
export interface GetCollectionBatchRouteRequestBody {
  collectionsToFetch: ({ collectionId: NumberType } & GetMetadataForCollectionRequestBody & GetAdditionalCollectionDetailsRequestBody)[],
}
```

```typescript
export interface GetCollectionBatchRouteSuccessResponse<T extends NumberType> {
  collections: BitBadgesCollection<T>[]
}
```

**Step 2: Prepare Your Request** Construct a JSON request body resembling the provided sample request. Include the following details:

**collectionsToFetch**: An array of objects, each representing a collection you wish to retrieve. For each collection:

* **collectionId**: The ID of the collection you intend to retrieve.
* **metadataToFetch**: An object specifying the desired metadata.
  * You can set **doNotFetchCollectionMetadata** to `true` to skip fetching collection metadata.
  * You can also retrieve specific metadata using the following fields (note there is a limit for how many you can fetch at once - currently 250):
    * **metadataIds**?: NumberType\[] | UintRange\<NumberType>\[];
    * **uris**?: string\[];
    * **badgeIds**?: NumberType\[] | UintRange\<NumberType>\[];
*   **viewsToFetch**: An array of objects specifying additional views of the collection to be fetched.

    ```typescript
    {
        viewKey: 'latestActivity' | 'latestAnnouncements' | 'latestReviews' | 'owners' | 'merkleChallenges' | 'approvalsTrackers';
        bookmark: string; //bookmark returned by the previous request
    }[];
    ```

    * Each view will return the next corresponding 25 results. The **bookmark** is what you need to pass into the following request. The **ids** will correspond to the IDs for the view. You can find the actual doc for each ID in the corresponding field (e.g. reviews: \[] for the "latestReviews" ids). **hasMore** will be false when all docs for the view are fetched.

    ```json
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
    ```
* **fetchTotalAndMintBalances**: Set to `true` if you desire to retrieve total and mint balances.
  * This will fetch the total and unminted balances and add them to the returned **owners** array.&#x20;



**Step 3: Send the Request** Utilize your chosen method (e.g., programming languages such as JavaScript with libraries like axios or fetch, or API testing tools like Postman) to dispatch a POST request to the endpoint `/api/v0/collection/batch` along with the prepared JSON request body.

**Step 4: Handle the Response** The response will encompass an array of collections featuring the requested details. Each collection object will encompass various attributes and timelines along with their respective data.

**Step 5: Pagination / Fetching More** In the previous response, you will have received a bookmark for each view within the `views` field. To obtain the subsequent page, employ the bookmark received from the last page in Step 2 for `viewsToFetch`.

**IMPORTANT**: Remember that each retrieval is confined to what is stipulated in the query options. It is your responsibility to cache and append the data to your previous responses. For a reference, consult the updateCollection function [here](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/collections/CollectionsContext.tsx) for reference.



#### Example Response

<pre class="language-json"><code class="lang-json"><strong>{
</strong>    "collections": [
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
</code></pre>


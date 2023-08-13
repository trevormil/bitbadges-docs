# User Tutorials

We refer you to the [Collections Tutorial](collections-tutorials.md). It is essentially the same as fetching details for users.

Simply replace the route and query options with the following:

**POST /api/v0/user/batch**

```typescript
export interface GetAccountsRouteRequestBody {
  accountsToFetch: AccountFetchDetails[],
}
```

```typescript
export type AccountFetchDetails = {
  address?: string,
  username?: string,
  fetchSequence?: boolean,
  fetchBalance?: boolean,
  viewsToFetch?: {
    viewKey: 'latestActivity' | 'latestAnnouncements' | 'latestReviews' | 'badgesCollected',
    bookmark: string,
    // mangoQuerySelector?: nano.MangoSelector
    // TODO: Allow users to specify their own mango query selector here. For now, we map the viewKey to a mango query selector.
  }[],
}

```



**Example Response**



```json
{
    "accounts": [
        {
            "_id": "",
            "seenActivity": 1691845143945,
            "discord": "trevormil#5747",
            "resolvedName": "",
            "publicKey": "Ag3SzcvRj/U4dXwN8APIpLzj5TyiR8OTZJwoThMN7XBt",
            "sequence": "11",
            "accountNumber": "10",
            "chain": "Ethereum",
            "cosmosAddress": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
            "address": "0xb246a3764d642BABbd6b075bca3e77E1cD563d78",
            "balance": {
                "amount": "1000",
                "denom": "badge"
            },
            "airdropped": true,
            "collected": [
                {
                    "_id": "5:cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
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
                                    "start": "1691802000000",
                                    "end": "1723338000000"
                                }
                            ]
                        }
                    ],
                    ...
                },
                ...
            ],
            "activity": [
                {
                    "_id": "collection-5:6214-0-0",
                    "from": "Mint",
                    "to": [
                        "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x"
                    ],
                    "balances": [
                        {
                            "amount": "1",
                            "ownershipTimes": [
                                {
                                    "start": "1691802000000",
                                    "end": "1723338000000"
                                }
                            ],
                            "badgeIds": [
                                {
                                    "start": "1",
                                    "end": "100"
                                }
                            ]
                        }
                    ],
                    "method": "Transfer",
                    "block": "6214",
                    "collectionId": "5",
                    "timestamp": "1691798701549",
                    "precalculationDetails": {
                        "approvalId": "1fd86e356afe6a2c06d2447cf43778022e96d084195d740609e9c746feca34dd",
                        "approvalLevel": "collection"
                    }
                },
                {
                    "_id": "collection-6:7693-0-0",
                    "from": "Mint",
                    "to": [
                        "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x"
                    ],
                    "balances": [
                        {
                            "amount": "1",
                            "ownershipTimes": [
                                {
                                    "start": "1691802000000",
                                    "end": "1723338000000"
                                }
                            ],
                            "badgeIds": [
                                {
                                    "start": "1",
                                    "end": "100"
                                }
                            ]
                        }
                    ],
                    "method": "Transfer",
                    "block": "7693",
                    "collectionId": "6",
                    "timestamp": "1691800261682",
                    "precalculationDetails": {
                        "approvalId": "32fa808f409afe9339855800d1f63d122a5dd60f44f571527ccb833b962e62e4",
                        "approvalLevel": "collection"
                    }
                },
                {
                    "_id": "collection-7:9352-0-0",
                    "from": "Mint",
                    "to": [
                        "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x"
                    ],
                    "balances": [
                        {
                            "amount": "1",
                            "ownershipTimes": [
                                {
                                    "start": "1691802000000",
                                    "end": "1723338000000"
                                }
                            ],
                            "badgeIds": [
                                {
                                    "start": "1",
                                    "end": "10"
                                }
                            ]
                        }
                    ],
                    "method": "Transfer",
                    "block": "9352",
                    "collectionId": "7",
                    "timestamp": "1691802012586",
                    "precalculationDetails": {
                        "approvalId": "9584780102bad52f1840502af441c0a2af8f00fda21183dd01bff38a36c2f8ef",
                        "approvalLevel": "collection"
                    }
                }
            ],
            "announcements": [
                {
                    "_id": "collection-6:3761630034074593",
                    "method": "Announcement",
                    "collectionId": "6",
                    "announcement": "HELLO!",
                    "from": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
                    "timestamp": "1691800369288",
                    "block": "7795"
                },
                {
                    "_id": "collection-6:7169529591936119",
                    "method": "Announcement",
                    "collectionId": "6",
                    "announcement": "testing!!!!",
                    "from": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
                    "timestamp": "1691800393922",
                    "block": "7819"
                }
            ],
            "reviews": [],
            "merkleChallenges": [],
            "approvalsTrackers": [],
            "views": {
                "latestActivity": {
                    "ids": [
                        "collection-5:6214-0-0",
                        "collection-6:7693-0-0",
                        "collection-7:9352-0-0"
                    ],
                    "type": "Activity",
                    "pagination": {
                        "bookmark": "g2wAAAACaAJkAA5zdGFydGtleV9kb2NpZG0AAAAVY29sbGVjdGlvbi03OjkzNTItMC0waAJkAAhzdGFydGtleWwAAAABbgYAqgtC54kBamo",
                        "hasMore": false
                    }
                },
                "badgesCollected": {
                    "ids": [
                        "5:cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
                        "6:cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
                        "7:cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x"
                    ],
                    "type": "Balances",
                    "pagination": {
                        "bookmark": "g1AAAACOeJzLYWBgYMpgSmHgKy5JLCrJTq2MT8lPzkzJBYrrm1sl5xfn5hcbZqcVGVUkZqWUFZuYZRiUlBQmppSaGuRllFpUmJQZlCSn5ZkUGFSAjOGAGUOOAVkAnxsvFQ",
                        "hasMore": false
                    }
                },
                "latestAnnouncements": {
                    "ids": [
                        "collection-6:3761630034074593",
                        "collection-6:7169529591936119"
                    ],
                    "type": "Announcements",
                    "pagination": {
                        "bookmark": "g2wAAAACaAJkAA5zdGFydGtleV9kb2NpZG0AAAAdY29sbGVjdGlvbi02OjcxNjk1Mjk1OTE5MzYxMTloAmQACHN0YXJ0a2V5bAAAAAFuBgDCWCnniQFqag",
                        "hasMore": false
                    }
                },
                "latestReviews": {
                    "ids": [],
                    "type": "Reviews",
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

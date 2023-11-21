# Fetching Users

**Notes**

* This is very similar to the [Collections Tutorial](fetching-collections.md).
* Addresses can be in any supported chain's format



**Tutorial: Making a POST Request to /api/v0/user/batch**

**Step 1: Understand the Route** The POST route `/api/v0/user/batch` is employed to retrieve information about multiple users in a collective manner. This route allows you to define the specific details you want to fetch for each user.

**Step 2: Prepare Your Request** Create a JSON request body similar to the provided sample request. Include the following details:

* `accountsToFetch`: An array of objects, each representing user account details you wish to retrieve. For each account:
  * `address`: The user's address, if available.
  * `username`: The user's username, if available.
  * `fetchSequence`: Set to `true` if you want to fetch the user's sequence information.
  * `fetchBalance`: Set to `true` if you want to fetch the user's $BADGE balance information.
  *   `viewsToFetch`: An array of objects specifying additional views to be fetched for the user.

      ```typescript
      {
          viewKey: 'latestActivity' | 'latestAnnouncements' | 'latestReviews' | 'badgesCollected',
          bookmark: string,
      }[]
      ```

      You can specify the desired view using `viewKey`, and provide the corresponding `bookmark` from the previous response.

**Step 3: Send the Request** Using your chosen approach (such as using programming languages like JavaScript with libraries like axios or fetch, or utilizing API testing tools like Postman), send a POST request to the `/api/v0/user/batch` endpoint with the prepared JSON request body.

**Step 4: Handle the Response** The response will contain an array of user account details with the requested information. Each account object will include various attributes and the timelines relevant to their data.

**Step 5: Pagination / Fetching More** If applicable, in the previous response, you might have received bookmarks for each view under the `viewsToFetch` field. To retrieve the next page of results, utilize the bookmark received from the last page in Step 2 for the corresponding `viewsToFetch`.

**IMPORTANT**: Remember that the retrieval is limited to what you specify in the query options. You are responsible for caching and appending data to your previous responses.&#x20;



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
                ...
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

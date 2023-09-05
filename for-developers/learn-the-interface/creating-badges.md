# Creating Badges

This will walk you through the process of creating badges and maintaining / defining their total supply over time.&#x20;

**Initial Creation**

During the initial creation transaction (MsgUpdateCollection with Collection ID 0), you can specify the badge amounts and supplys you wish to create via the BadgesToCreate field. This will create and transfer these badges to the special "Mint" address.&#x20;

The initial creation transaction's BadgesToCreate are considered "free" and not locked by any permissions. The creator can mint as much as they want in this initial transaction. For example,

```json
{
  "creator": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
  "collectionId": "0",
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
          "start": "1692122400000",
          "end": "1723658400000"
        }
      ]
    }
  ],
  ...
}
```

From the "Mint" address, you can transfer according to the collection's approved transfers (see [Distributing Badges](distributing-badges.md)).&#x20;

The ownership times will be the times that the badge scan be owned or in circulation.

**CanCreateMoreBadges Permission**

Over time, you may want to add more badges to the collection. However, the BadgesToCreate are no longer considered "free", as they were in the initial creation transaction. They must obey the CanCreateMoreBadges permission (see Locking Total Supply below). This is also defined in the MsgUpdateCollection. An example is as follows:

```json
 "collectionPermissions": {
    "canArchiveCollection": [],
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
```

**Locking Total Supply**

Pre-Reading: [Permissions](../concepts/permissions.md)

The CanCreateMoreBadges permission defines what badges and at what ownership times, the manager can create more badges (no restriction on amounts).&#x20;

By default (when CanCreateMoreBadges is empty \[]), the manager can mint as many badges as they want at any time they want because permissions are by default allowed if unhandled. Creating more badges can simply be done via another MsgUpdateCollection call and specifying new amounts for BadgesToCreate.

CanCreateMoreBadges can also be set to be permitted or forbidden for specific badge IDs and ownership times. Remember, we take a first-match only approach.

For example, the following value for CanCreateMoreBadges locks the entire supply of badges for any ID and ownership time forever.

```typescript
{
  defaultValues: {
    badgeIds: [{ start: 1, end: GO_MAX_UINT_64 }],
    ownershipTimes: [{ start: 1, end: GO_MAX_UINT_64 }],
    permittedTimes: [],
    forbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }],
  },
  combinations: [
    {
      badgeIdsOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      ownershipTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      permittedTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      forbiddenTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      }
    }
  ]
}

```

The following only restricts the manager from creating Badge ID 1. Everything else is allowed.

```go
{
  defaultValues: {
    badgeIds: [{ start: 1, end: 1 }],
    ownedTimes: [{ start: 1, end: GO_MAX_UINT_64 }],
    permittedTimes: [],
    forbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }],
  },
  combinations: [
    {
      badgeIdsOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      ownershipTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      permittedTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      forbiddenTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      }
    }
  ]
}
```

And, the following forbids the manager from creating new supply of Badge ID 1 that is ownable for the year 2024 (e.g. a token unlock).

```typescript
{
  defaultValues: {
    badgeIds: [{ start: 1, end: 1 }],
    ownedTimes: [{ start: START_2024, end: END_2024 }],
    permittedTimes: [],
    forbiddenTimes: [{ start: 1, end: GO_MAX_UINT_64 }],
  },
  combinations: [
    {
      badgeIdsOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      ownershipTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      permittedTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      },
      forbiddenTimesOptions: {
        invertDefault: false,
        allValues: false,
        noValues: false
      }
    }
  ]
};
```

As a reminder, remember that permissions are by default ALLOWED if not explicitly forbidden and also FIRST-MATCH-ONLY (see [Permissions](../concepts/permissions.md)).

**Need to Restrict Amounts As Well?**

You may have noticed that the CanCreateMoreBadges is an all-or-nothing permission, either the  manager can create an unlimited amount of badges for some ownership times or none at all. Sometimes, a use case may require to restrict the amounts that can be created as well, such as with token unlocks.&#x20;

For this, you should use the "Mint" address as an escrow and precreate all badges that you may potentially need in the future.&#x20;

You should 1) create the badges which are sent to "Mint" address as shown above, 2) lock the ability to create more badges as shown above, and then 3) restrict the release of badges using the CollectionApprovedTransfers and CanUpdateCollectionApprovedTransfers to keep badges escrowed in the "Mint" address for the desired times and amounts (see [Distributing Badges](distributing-badges.md)).

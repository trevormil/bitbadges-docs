# ➕ Creating Badges

**Getting Started**

To create a brand new collection, you will be creating a [MsgUpdateCollection](cosmos-msgs.md) with **collectionId** set to 0. The collectionId will be assigned at execution time. Subsequent updates to the collection will also be through MsgUpdateCollection but specifying the actual collectionId.

**Creating Badges**

During the initial creation transaction (MsgUpdateCollection with Collection ID 0), you can specify the badge amounts and supplys you wish to create via the **badgesToCreate** field. This will create and transfer these badges to the "Mint" address. No one controls the "Mint" address. It can only be transferred out of, not transferred to.

The initial creation transaction's **badgesToCreate** are considered "free" and not locked by any permissions. The creator can mint as much as they want in this initial transaction.&#x20;

The **ownershipTimes** will be the times that the badges can be owned aka in circulation. The **badgeIds** are the IDs of each unique badge. Badge IDs must start at 1 and have no gaps.

For example,

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

From the "Mint" address, you can transfer according to the collection's approvals as explained in [Approvals](approvals.md) and [Approval Criteria](approval-criteria.md).

**CanCreateMoreBadges Permission**

Over time, you may want to add more badges to the collection. However, in later MsgUpdateCollection transactions, the **badgesToCreate** are no longer considered "free", as they were in the initial creation transaction. They must obey the **canCreateMoreBadges** permission (see Locking Total Supply below). This is also defined in the MsgUpdateCollection via **collectionPermissions**.&#x20;

Note that all permissions are only executable by the current manager and no one else.

An example permission is as follows. This locks any badges from ever being created in the future.  **canCreateMoreBadges** can also be more fine-grained and set to be permitted or forbidden for specific badge IDs and ownership times. See [Permissions](permissions.md) for more details on how permissions work.&#x20;

```json
 "collectionPermissions": {
    "canArchiveCollection": [],
    "canCreateMoreBadges": [
      {
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
  }
```

**Permission vs Escrow**

There are two ways to handle creating and defining a total supply.

**Permission:** The first, as explained above, is via the **canCreateMoreBadges** permission. However, this permission has no amount restriction. If the manager is approved, they can create an unlimited amount. if they are disapproved, they cannot create any.

**Escrow:** Some collections may need restrictions on how many badges can be created (e.g. token unlocks). For this, you should use the "Mint" address as an escrow and precreate all badges that you may potentially need in the future then lock the permission.

You should 1) create the badges which are sent to "Mint" address as shown above, 2) lock the ability to create more badges as shown above, and then 3) restrict the release of badges using the collection's transferability to keep badges escrowed in the "Mint" address for the desired times and amounts.

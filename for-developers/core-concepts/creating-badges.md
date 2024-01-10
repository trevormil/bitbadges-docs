# ➕ Creating Badges

**Balance Types**

All collections will "create" some supply of badges on-chain; however, the interpretation of that supply is slightly different dependent on the balance storage method.

* Off-Chain Indexed: The badges created on-chain will permanently live in the "Mint" address and this value will be used as the "maximum supply". If the host server for the balances off-chain tries to assign more than what is created on-chain, an error should be thrown.
* Off-Chain Non-Indexed: Similar to indexed, the badges created on-chain will permanently live in the "Mint" address, but there is no verifiable maximum supply for non-indexed collections. The values here are simply used to denote the expected badge IDs for the collection.
* Standard On-Chain: Badges created on-chain can be owned by users. This can be in the "Mint" address or user addresses The "Mint" address can only send badges, not receive them. Badges will initially be sent to the "Mint" address when created out of thin air. The circulating supply will live out according to the permissions, approvals, and transfers of the collection.

**Default Starting Balances**

For standard on-chain balances (not applicable to off-chain), you can also define a default starting balance for all addresses. Upon first interaction, each address is given the default balances. Since an infinite number of addresses can be created theoretically, there is theoretically an infinite supply of badges, so this is only used in specific use cases.

The more common way of creating badges is explained below.

**Creating Badges Out of "Thin Air"**

During creation and update transaction ([MsgCreateCollection](../create-and-broadcast-txs/cosmos-sdk-msgs/msgcreatecollection.md) and [MsgUpdateCollection](../create-and-broadcast-txs/cosmos-sdk-msgs/msgupdatecollection.md)), you can specify the badge amounts and supplys you wish to create via the **badgesToCreate** field. This will create these badges out of thin air and transfer these badges to the "Mint" address. No one controls the "Mint" address. It can only be transferred out of, not transferred to.&#x20;

The initial creation transaction's **badgesToCreate** are considered "free" and not locked by any permissions. The creator can mint as much as they want in this initial transaction. Following transactions must obey the **canCreateMoreBadges** manager permission.

The **ownershipTimes** will be the times that the badges can be owned aka in circulation. The **badgeIds** are the IDs of each unique badge. Badge IDs must start at 1 and have no gaps.

For example,

```json
{
  "creator": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
  ...
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
  "collectionPermissions": {
    "canCreateMoreBadges": [....],
    ...
  }
}
```

From the "Mint" address, you can customize the transferability according to the collection's approvals as explained in [Approvals](transferability-approvals.md) and [Approval Criteria](approval-criteria/).

**CanCreateMoreBadges Permission**

Over time, you may want to add more badges to the collection. However, in later MsgUpdateCollection transactions, the **badgesToCreate** are no longer considered "free", as they were in the initial creation transaction. They must obey the **canCreateMoreBadges** permission (see Locking Total Supply below). This is also defined in the MsgUpdateCollection via **collectionPermissions**.

Note that all permissions are only executable by the current manager and no one else.

An example permission is as follows. This locks any badges from ever being created in the future. **canCreateMoreBadges** can also be more fine-grained and set to be permitted or forbidden for specific badge IDs and ownership times. See [Permissions](permissions.md) for more details on how permissions work.

```json
 "collectionPermissions": {
     ...
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
        "permanentlyPermittedTimes": [],
        "permanentlyForbiddenTimes": [
          {
            "start": "1",
            "end": "18446744073709551615"
          }
        ]
      }
    ],
    ...
  }
```

**Permission vs Escrow**

For standard on-chain balances, there are two ways to handle restricting the total supply.

**Permission:** The first, as explained above, is via the **canCreateMoreBadges** permission. However, this permission has no amount restriction. If the manager is approved, they can create an unlimited amount. If they are disapproved, they cannot create any. Its all-or-nothing.

**Escrow:** Some collections may need restrictions on how many badges can be created (e.g. token unlocks). For this, you should use the "Mint" address as an escrow by creating all badges that you may potentially need in the future, lock the **canCreateMoreBadges** permission, and use the collection approvals to keep everything escrowed as intended in the "Mint" address.

You should 1) create the badges which are sent to "Mint" address as shown above, 2) lock the ability to create more badges as shown above, and then 3) restrict the release of badges using the collection's transferability to keep badges escrowed in the "Mint" address for the desired times and amounts.

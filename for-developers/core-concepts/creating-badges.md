# ➕ Creating Badges

**Balance Types**

All collections will "create" some supply of badges on-chain; however, the interpretation of that supply is slightly different dependent on the balance storage method.&#x20;

* Off-Chain: The vallid badge ID range (e.g. 1-100) will be specified on-chain and managed through permissions. There is no "enforceable" total supply.
* Standard On-Chain: Badges created on-chain can be owned by users. The "Mint" address has unlimited balances, can only send badges, not receive them. The circulating supply will live out according to the permissions, approvals, and transfers of the collection from the Mint address.

**Creating Badges**

During creation and update transaction ([MsgCreateCollection](../create-and-broadcast-txs/cosmos-sdk-msgs/msgcreatecollection.md) and [MsgUpdateCollection](../create-and-broadcast-txs/cosmos-sdk-msgs/msgupdatecollection.md)), you can specify the badge IDs ranges that are valid via the **badgeIdsToAdd** field.&#x20;

The Mint address has unlimited balances. No one controls the "Mint" address. It can only be transferred out of, not transferred to.  The **ownershipTimes** will be the times that the badges can be owned aka in circulation. The **badgeIds** are the IDs of each unique badge. Badge IDs must start at 1 and have no gaps.

The total supply is governed by the transfers FROM the Mint address. This is done via approvals (collection approvals) and the permissions (can update transferability?). You can customize the transferability according to the collection's approvals as explained in [Approvals](transferability-approvals.md) and [Approval Criteria](approval-criteria/).

**Escrow**

Some collections may need restrictions on how many badges can be created (e.g. token unlocks). For this, you should use the "Mint" address as an escrow by creating all badges that you may potentially need in the future, lock the necessary permission, and use the collection approvals to keep everything escrowed as intended in the "Mint" address.

Or, you can "mint" badges and use another address as the escrow. It can be entirely customized by you.

**Example**

```json
{
  "creator": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
  ...
  "badgeIdsToAdd": [
      {
        "start": "1",
        "end": "100"
      }
  ],
  ...
  "collectionPermissions": {
    "canCreateMoreBadges": [....],
    ...
  }
}
```




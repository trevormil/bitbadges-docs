# ➕ Valid Badge IDs

#### badgeIdsToAdd / validBadgeIds

* Regardless of balance storage method, the range of valid badge IDs (e.g., 1-100) must be defined on-chain in the core collection details.&#x20;
* Once an ID is added, it cannot be unadded. IDs must start at 1 and have no gaps.
* This range is specified during collection creation or update. Updates must obey the **canUpdateValidBadgeIds** permission, which can freeze or permit adding certain IDs.
* Note this is separate from the circulating supply. The circulating supply is managed through on-chain transfers / approvals or off-chain allocations (whichever is applicable). The circulating supply must obey the valid badge IDs defined on-chain.

**Creating Badges**

During creation and update transaction ([MsgCreateCollection](../../cosmos-sdk-msgs/x-badges/msgcreatecollection.md) and [MsgUpdateCollection](../../cosmos-sdk-msgs/x-badges/msgupdatecollection.md)), you can specify the badge IDs ranges that are valid via the **badgeIdsToAdd** field.

**Example**

```json
{
  "creator": "bb1kfr2xajdvs46h0ttqadu50nhu8x4v0tc2wajnh",
  ...
  "badgeIdsToAdd": [
      {
        "start": "1",
        "end": "100"
      }
  ],
  ...
  "collectionPermissions": {
    "canUpdateValidBadgeIds": [....],
    ...
  }
}
```

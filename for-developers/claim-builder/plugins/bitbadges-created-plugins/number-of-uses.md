# Number of Uses

**Plugin ID: `numUses`**

This plugin restricts the number of times specific addresses can claim and the maximum overall amount of times users can claim.

### Public Parameters

#### Public Parameters

-   **maxUses**: The maximum number of uses allowed for the code overall.
-   **hideCurrentState**: If true, we will NOT reveal the state to users by default.&#x20;
    -   If you are claim creator / authorized viewer, use the fetch private parameters flag and it will return the state.
    -   The **publicState** will just be an empty {} by default.
    -   Note that this hides it within the context of the claim, but if the claim action is public (e.g. public badge assignment, public lists), the state may still be leaked there.
-   **displayAsUnlimited:** This is a UI display option. When enabled, we label the claim as "unlimited uses" to the user. Note this does NOT actually make it unlimited behind the scenes. We still track normally. If you use this option, we recommend setting maxUses to something crazy that will never be met like > 1000000000 uses.
    -   Please use with hideCurrentState = true.
    -   This is typically used with Sign In with BitBadges claims.

**Private Parameters**

None

### State

State is maintained by incrementing the numUses by 1 **every** claim by any user. For each user, we track the claim numbers assigned to that user.

```
{
  claimedUsers: {
    "bb1...": [0, 1]
  },
  numUses: 0
}
```

#### Default State

The default state of the plugin is defined as follows:

```
{
  claimedUsers: {},
  numUses: 0
}
```

**Public State**

State is made public as-is.

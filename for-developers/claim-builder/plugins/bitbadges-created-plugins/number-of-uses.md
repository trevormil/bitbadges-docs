# Number of Uses

**Plugin ID: `numUses`**

This plugin restricts the number of times specific addresses can claim and the maximum overall amount of times users can claim.

### Public Parameters

#### Public Parameters

* **maxUses**: The maximum number of uses allowed for the code overall.

**Private Parameters**

None

### State

State is maintained by incrementing the numUses by 1 **every** claim by any user. For each user, we track the claim numbers assigned to that user.

```
{
  claimedUsers: {
    "cosmos1...": [0, 1]
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

# Whitelist

### Plugin ID: `whitelist`

This plugin restricts claims to be received by users who are on a predefined whitelist.

With this plugin, we reuse the [Address List ](../../../core-concepts/address-lists-lists.md)types. IDs will be the list ID. Only one list should be defined out of the parameters. The lsit can either be made public with the claim or kept private.

### Public Parameters

* **list**: An [Address List ](../../../core-concepts/address-lists-lists.md)specifying who can claim vs not.
* **listId**: The  [Address List ](../../../core-concepts/address-lists-lists.md)ID that points to a valid address list for who can claim vs not.
* **maxUsesPerAddress**: The maximum number of uses allowed for each address.

### Private Parameters

* **list**: An [Address List ](../../../core-concepts/address-lists-lists.md)specifying who can claim vs not.
* **listId**: The  [Address List ](../../../core-concepts/address-lists-lists.md)ID that points to a valid address list for who can claim vs not.

### State

State is maintained by incrementing the numUses by 1 **every** claim by any user. For each user, we track the claim numbers assigned to that user.

```
{
  addresses: {
    "cosmos1...": 10 // claimed 10 times
  }
}
```

#### Default State

The default state of the plugin is defined as follows:

```
{
  addresses: {}
}
```

**Public State**

State is made public as-is.

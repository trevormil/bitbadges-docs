# Whitelist

### Plugin ID: `whitelist`

This plugin restricts claims to be received by users who are on a predefined whitelist.

With this plugin, we reuse the [Address List ](../../../core-concepts/address-lists-lists.md)types. IDs will be the list ID. Only one list should be defined out of the parameters. The lsit can either be made public with the claim or kept private.

### Public Parameters

* **list**: An [Address List ](../../../core-concepts/address-lists-lists.md)specifying who can claim vs not.
* **listId**: The  [Address List ](../../../core-concepts/address-lists-lists.md)ID that points to a valid address list for who can claim vs not.

### Private Parameters

* **list**: An [Address List ](../../../core-concepts/address-lists-lists.md)specifying who can claim vs not.
* **listId**: The  [Address List ](../../../core-concepts/address-lists-lists.md)ID that points to a valid address list for who can claim vs not.

### State

State is not maintained for this plugin as it relies solely on the provided whitelist for validation.\

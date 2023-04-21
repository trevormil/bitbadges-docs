# 🔁 Transfer Mappings

Transfer mappings define a mapping of transfers from one set of **Addresses** to another. This is used for defining the transferability of the collection and the manager's approved transfers.

A (toAddress, fromAddress) address pair is considered in the transfer mapping if:

* toAddress is in the to **Addresses**
* fromAddress is in the the from **Addresses**

```protobuf
message Addresses {
    repeated IdRange accountIds = 1;
    uint64 options = 2;
}

//TransferMapping defines a mapping of transfers from one set of addresses to another.
message TransferMapping {
    Addresses from = 1;
    Addresses to = 2;
}
```

For example, if we have the following transfer mapping, the (toAddress = 5, fromAddress = 10) would be considered in the transfer mapping.

```json
{
    from: {
        accountIds: [{start: 1, end: 100}],
        options: 0,
    },
    from: {
        to: [{start: 1, end: 100}],
        options: 0,
    },
}
```

### Options

The options field allows one to include / exclude the manager if desired. Since the manager's account ID is not fixed and can potentially be transferred, this allows the functionality for the manager's address to always be included / excluded, if desired.

If options == 0, we do nothing.&#x20;

If options == 1, we include the manager's ID in accountIds, if not already included.&#x20;

If options == 2, we exclude the manager's ID.

### Full / Complete Transfer Mappings

You may need to represent a transfer mapping of all possible account IDs (e.g. making a badge non-transferable requires specifying a transfer mapping with all possible accountIds).

The standardized way to represent this is to use the following transfer mapping:

```json
{
    from: {
        accountIds: [{start: 0, end: TODO}],
        options: 0,
    },
    to: {
        accountIds: [{start: 0, end: TODO}],
        options: 0,
    }
}
```

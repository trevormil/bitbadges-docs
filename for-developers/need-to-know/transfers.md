# ➡ Transfers

This is the Transfers type object, used for minting and transferring badges. It transfers the badge balances defined in **balances** to each of the toAddresses.&#x20;

The **from** field will be defined separately (see [MsgTransferBadge](tx-msg-interfaces.md)).

```protobuf
message Transfers {
    repeated uint64 toAddresses = 1;
    repeated Balance balances = 2;
}
```

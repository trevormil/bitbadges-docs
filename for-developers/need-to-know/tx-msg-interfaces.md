# ✉ Tx Msg Interfaces

If you want to interact with the BitBadges blockchain, you will need to know about the different types of transactions offered. With the Cosmos SDK, transactions with different functionalities are defined as [messages](https://docs.cosmos.network/v0.46/core/transactions.html#messages). See [broadcasting transactions](../../sdk/broadcasting-and-signing-txs.md) for how to create and broadcast transactions.

The normal send function for $BADGE is **MsgSend** offered natively by the Cosmos SDK.

For BitBadges, we provide the following transaction types.&#x20;

Note the **creator** field is the sending user's cosmos address. This is auto-added by the blockchain.



**TODO Provide Examples**



**MsgNewCollection**

Creates a new badge collection. See [collections](collections.md) for information on each field.&#x20;

**transfers** offers a way to distribute badges directly upon creation. The creator pays all transfer fees.

**badgeSupplys\[]** creates x**amount** of badges each with a supply of **supply.** Ex: x10 badges of supply 1 would create 10 non-fungible badges with IDs 1-10.&#x20;

```protobuf
message MsgNewCollection {
    string creator = 1; 
    string collectionUri = 2;
    repeated BadgeUri badgeUris = 3;
    uint64 permissions = 4;
    string bytes = 5;
    repeated TransferMapping disallowedTransfers = 6;
    repeated TransferMapping managerApprovedTransfers = 7;
    uint64 standard = 8; 
    repeated BadgeSupplyAndAmount badgeSupplys = 9;
    repeated Transfers transfers = 10;
    repeated Claim claims = 11;
}
```

```protobuf
message BadgeSupplyAndAmount {
    uint64 supply = 1;
    uint64 amount = 2;
}
```

**MsgMintBadge**

Add badges to a collection and/or distribute unminted badges.

```protobuf
message MsgMintBadge {
    string creator = 1;
    uint64 collectionId = 2;
    repeated BadgeSupplyAndAmount badgeSupplys = 3;
    repeated Transfers transfers = 4;
    repeated Claim claims = 5;
    string collectionUri = 6;
    repeated BadgeUri badgeUris = 7;
}
```

**badgeSupplys** will do the same as MsgNewCollection but the new badge IDs will start at whatever the collection's **nextBadgeId** field is currently at.

**collectionUri** and **badgeUris** will set these fields for the collection as defined in this Msg (if not defined, will not set). Note this sets exactly as specified (i.e. overwrites existing URIs).

Note that if the CanUpdateUris permission is turned off / disabled, the existing collectionUri and badgeUris cannot be edited, so it will give an error if you try and change anything. For badgeUris, you are allowed to append new BadgeUris for the newly created badges, but you can not edit existing ones. You may leave either or both blank to say not to update anything.

**MsgClaimBadge**

Claim a badge that is currently claimable. If needed, provide a whitelistProof or codeProof with a valid SHA256 Merkle path to the respective root.&#x20;

```protobuf
message MsgClaimBadge {
    string creator = 1;
    uint64 claimId = 2;
    uint64 collectionId = 3;
    ClaimProof whitelistProof = 4;
    ClaimProof codeProof = 5;
}
```

```protobuf
message ClaimProofItem {
    string aunt = 1;
    bool onRight = 2;
}

message ClaimProof {
    string leaf = 1;
    repeated ClaimProofItem aunts = 2;
}
```

**TODO Provide Example**



**MsgTransferBadge**

Transfers the badges defined in [**transfers**](transfers.md) from the provided **from** account ID to those defined in the **transfers** field. **from** can be another user besides the calling user, but they will need adequate approvals.

```protobuf
message MsgTransferBadge {
    string creator = 1;
    uint64 collectionId = 2;
    uint64 from = 3;
    repeated Transfers transfers = 4;
}
```

**MsgDeleteCollection**

Deletes a collection.

```protobuf
message MsgDeleteCollection {
    string creator = 1;
    uint64 collectionId = 2;
}
```

**MsgRegisterAddresses**

Register addresses on the blockchain. Used if a user is new and does not have an account ID yet.

```protobuf
message MsgRegisterAddresses {
  string creator = 1;
  repeated string addressesToRegister = 2;
}
```

**MsgRequestTransferManager**

Request the manager role to be transferred to you. If the manager role is to be transferred, the recipient needs to make a request first.

```protobuf
message MsgRequestTransferManager {
  string creator = 1;
  uint64 collectionId = 2;
  bool addRequest = 3;
}
```

**addRequest** defines whether to add a request (true) or cancel a request (false).



**MsgTransferManager**

Transfer the manager role to a new user. If the manager role is to be transferred, the recipient needs to make a request first.

```protobuf
message MsgTransferManager {
  string creator = 1;
  uint64 collectionId = 2;
  uint64 address = 3;
}
```

**MsgSetApproval**

Sets an approval for a specific address to transfer badges on your behalf.

```protobuf
message MsgSetApproval {
    string creator = 1;
    uint64 collectionId = 2;
    uint64 address = 3; //The address that are approved to transfer the balances.
    repeated Balance balances = 4; //approval balances for every badgeId
}
```

**MsgUpdateBytes**

Updates the bytes field.

```protobuf
message MsgUpdateBytes {
  string creator = 1;
  uint64 collectionId = 2;
  string newBytes = 3;
}
```

**MsgUpdateUris**

Updates the collectionUri and badgeUris.

```protobuf
message MsgUpdateUris {
  string creator = 1;
  uint64 collectionId = 2;
  string collectionUri = 3;
  repeated BadgeUri badgeUris = 4;
}
```

**MsgUpdatePermissions**

Updates the permissions for the collection. See [permissions](permissions.md).

```protobuf
message MsgUpdatePermissions {
  string creator = 1;
  uint64 collectionId = 2;
  uint64 permissions = 3;
}
```

**MsgUpdateDisallowedTransfers**

Updates the disallowed transfers field.

```protobuf
message MsgUpdateDisallowedTransfers {
  string creator = 1;
  uint64 collectionId = 2;
  repeated TransferMapping disallowedTransfers = 3;
}
```

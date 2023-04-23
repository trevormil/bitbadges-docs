# ✉ Msgs

In Cosmos SDK, Msgs are messages that represent actions to be executed on the blockchain, such as sending tokens or creating a new account. Each Cosmos SDK module can define their own Msg types based on their specific functionality. For example, the staking module defines Msgs for actions related to staking, such as creating a validator or delegating tokens. The governance module defines Msgs for submitting and voting on proposals to change the parameters of the blockchain. This modular approach allows developers to add and remove functionality as needed, and enables interoperability between different blockchains that use the Cosmos SDK.&#x20;

The BitBadges blockchain utilizes various pre-written modules from the Cosmos SDK, including the staking module and bank module (a full list can be found in [app.go](https://github.com/BitBadges/bitbadgeschain/blob/master/app/app.go)). However, the x/badges module is the core functionality of BitBadges written by us, and within this module, all the Msg types that correspond to badges are defined.

The documentation for the pre-written modules can be found [here](https://docs.cosmos.network/main/modules). Some common Msgs from pre-written modules you may need include:

* MsgSend from the x/bank module - Send $BADGE
* MsgDelegate and MsgUndelegate from the x/staking module - Stake / Unstake $BADGE

## Submitting / Broadcasting Transactions

You can submit / broadcast your transactions (Msgs) to the BitBadges team's node via [this tutorial using the BitBadges SDK](../../sdk/broadcasting-and-signing-txs.md).&#x20;

Using the above tutorial is the recommended method, but you can also interact with other nodes (i.e. one you run yourself or other BitBadges nodes). See [https://docs.cosmos.network/main/run-node/interact-node](https://docs.cosmos.network/main/run-node/interact-node) for your options. There are plenty of resources out there for interacting with a Cosmos SDK node.

## Badge Msg Types

Below, we will provide the documentation for the Msgs from our x/badges module.&#x20;

### Overview

* [MsgNewCollection](msgs.md#msgnewcollection) - Creates a new badge collection and distributes badges.
* [MsgMintBadge](msgs.md#msgmintbadge) - Adds badges to a collection and distributes badges.
* [MsgClaimBadge](msgs.md#msgclaimbadge) - Claim a badge, if claimable
* [MsgTransferBadge](msgs.md#msgtransferbadge) - Transfer a badge to a new owner, if transferability allows.
* [MsgDeleteCollection](msgs.md#msgdeletecollection) - Deletes a collection, if permissions allow.
* [MsgRegisterAddresses](msgs.md#msgregisteraddresses) - Register a new address (assign it an account ID number).
* [MsgTransferManager](msgs.md#msgtransfermanager) - Transfer the role of the manager
* [MsgRequestTransferManager](msgs.md#msgrequesttransfermanager) - Request the role of the manager to be transferred
* [MsgSetApproval](msgs.md#msgsetapproval) - Approve another address to transfer on your behalf
* [MsgUpdatePermissions](msgs.md#msgupdatepermissions) - Update the manager's permissions
* [MsgUpdateUris](msgs.md#msgupdateuris) - Update the metadata URIs
* [MsgUpdateDisallowedTransfers](msgs.md#msgupdatedisallowedtransfers) - Update the transferability
* [MsgUpdateBytes](msgs.md#msgupdatebytes) - Update the bytes field

### Pre-Notes

* The **creator** field for each message is the transaction sender's cosmos address. This is automatically added by the blockchain. You will not need to specify this.
* You may see unrecognized types. In the following pages, we provide more detailed explanations for each type. We will provide a brief summary, but refer to the following pages for further details.

### **MsgNewCollection**

Creates a new badge collection.

**collectionUri** and **badgeUris** defined the metadata for the collection and badges.

**permissions** defines the manager's permissions for the collection

**bytes** allows for an arbitrary storage of bytes

**disallowedTransfers** defines the transferability of the collection

**managerApprovedTransfers** defines what transfer privileges the manager has (revoking, burning, etc). This overrides **disallowedTransfers.**

**standard** defines the standard this collection implements.

**transfers** and **claims** are for distributing / minting badges.

**badgeSupplys\[]** defines how many badges to create. Will create x**amount** of badges each with a supply of **supply.** Ex: x10 badges of supply 1 would create 10 non-fungible badges with IDs 1-10.&#x20;

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

### **MsgMintBadge**

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

### **MsgClaimBadge**

Claim a badge that is currently claimable. If needed, provide a whitelistProof or codeProof with a valid SHA256 Merkle path to the respective root. See [Creating Claims](../tutorials/creating-claims.md) for a tutorial.

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

### **MsgTransferBadge**

Transfers the badges defined in [**transfers**](transfers.md) from the provided **from** account ID to those defined in the **transfers** field. **from** can be another user besides the calling user, but they will need adequate approvals set.

```protobuf
message MsgTransferBadge {
    string creator = 1;
    uint64 collectionId = 2;
    uint64 from = 3;
    repeated Transfers transfers = 4;
}
```

### **MsgDeleteCollection**

Deletes a collection.

```protobuf
message MsgDeleteCollection {
    string creator = 1;
    uint64 collectionId = 2;
}
```

### **MsgRegisterAddresses**

Register addresses on the blockchain. Assigns a new user an account ID.

```protobuf
message MsgRegisterAddresses {
  string creator = 1;
  repeated string addressesToRegister = 2;
}
```

### **MsgRequestTransferManager**

Request the manager role to be transferred to you. If the manager role is to be transferred, the recipient needs to make a request first.

```protobuf
message MsgRequestTransferManager {
  string creator = 1;
  uint64 collectionId = 2;
  bool addRequest = 3;
}
```

**addRequest** defines whether to add a request (true) or cancel a request (false).



### **MsgTransferManager**

Transfer the manager role to a new user. If the manager role is to be transferred, the recipient needs to make a request first.

```protobuf
message MsgTransferManager {
  string creator = 1;
  uint64 collectionId = 2;
  uint64 address = 3;
}
```

### **MsgSetApproval**

Sets an approval for a specific address to transfer badges on your behalf.

```protobuf
message MsgSetApproval {
    string creator = 1;
    uint64 collectionId = 2;
    uint64 address = 3; //The address that are approved to transfer the balances.
    repeated Balance balances = 4; //approval balances for every badgeId
}
```

### **MsgUpdateBytes**

Updates the bytes field.

```protobuf
message MsgUpdateBytes {
  string creator = 1;
  uint64 collectionId = 2;
  string newBytes = 3;
}
```

### **MsgUpdateUris**

Updates the collectionUri and badgeUris.

```protobuf
message MsgUpdateUris {
  string creator = 1;
  uint64 collectionId = 2;
  string collectionUri = 3;
  repeated BadgeUri badgeUris = 4;
}
```

### **MsgUpdatePermissions**

Updates the permissions for the collection. See [permissions](permissions.md).

```protobuf
message MsgUpdatePermissions {
  string creator = 1;
  uint64 collectionId = 2;
  uint64 permissions = 3;
}
```

### **MsgUpdateDisallowedTransfers**

Updates the disallowed transfers field.

```protobuf
message MsgUpdateDisallowedTransfers {
  string creator = 1;
  uint64 collectionId = 2;
  repeated TransferMapping disallowedTransfers = 3;
}
```

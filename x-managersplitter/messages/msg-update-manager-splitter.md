# MsgUpdateManagerSplitter

## Overview

`MsgUpdateManagerSplitter` updates the permissions of an existing Manager Splitter entity. Only the admin address can update the manager splitter.

## Message Structure

```protobuf
message MsgUpdateManagerSplitter {
  option (cosmos.msg.v1.signer) = "admin";
  option (amino.name) = "managersplitter/UpdateManagerSplitter";

  // Admin address updating the entity.
  string admin = 1;

  // Address of the manager splitter to update.
  string address = 2;

  // New permissions to set.
  ManagerSplitterPermissions permissions = 3;
}
```

## Fields

### `admin` (string, required)

The admin address of the manager splitter. This must match the manager splitter's stored admin address. Must be a valid Bech32 address.

### `address` (string, required)

The address of the manager splitter to update. This is the module-derived address returned when the manager splitter was created. Must be a valid Bech32 address.

### `permissions` (ManagerSplitterPermissions, required)

The new permissions configuration to set. This completely replaces the existing permissions structure.

## ManagerSplitterPermissions Structure

```protobuf
message ManagerSplitterPermissions {
  PermissionCriteria canDeleteCollection = 1;
  PermissionCriteria canArchiveCollection = 2;
  PermissionCriteria canUpdateStandards = 3;
  PermissionCriteria canUpdateCustomData = 4;
  PermissionCriteria canUpdateManager = 5;
  PermissionCriteria canUpdateCollectionMetadata = 6;
  PermissionCriteria canUpdateValidTokenIds = 7;
  PermissionCriteria canUpdateTokenMetadata = 8;
  PermissionCriteria canUpdateCollectionApprovals = 9;
}
```

Each field is optional. If a permission is not set in the update, it will be removed (denied by default).

## PermissionCriteria Structure

```protobuf
message PermissionCriteria {
  // List of approved addresses that can execute this permission.
  repeated string approvedAddresses = 1;
}
```

### `approvedAddresses` (string\[], optional)

A list of Bech32 addresses that are approved to execute this permission. If empty or not provided, only the admin can execute this permission.

## Response

```protobuf
message MsgUpdateManagerSplitterResponse {}
```

An empty response indicates successful update.

## Usage Example

```json
{
  "admin": "bb1abc123...",
  "address": "bb1managersplitter...",
  "permissions": {
    "canUpdateCollectionMetadata": {
      "approvedAddresses": [...]
    },
    "canUpdateTokenMetadata": {
      "approvedAddresses": [
        "bb1..."
      ]
    }
  }
}
```

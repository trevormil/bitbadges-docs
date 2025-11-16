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

### `approvedAddresses` (string[], optional)
A list of Bech32 addresses that are approved to execute this permission. If empty or not provided, only the admin can execute this permission.

## Response

```protobuf
message MsgUpdateManagerSplitterResponse {}
```

An empty response indicates successful update.

## Validation

The message is validated to ensure:
1. The `admin` address is a valid Bech32 address
2. The `address` is a valid Bech32 address
3. The manager splitter exists
4. The `admin` matches the manager splitter's stored admin address

## Authorization

Only the admin address can update the manager splitter. If the `admin` field doesn't match the manager splitter's stored admin, the transaction will fail with an unauthorized error.

## State Changes

The manager splitter's permissions are completely replaced with the new permissions structure. This means:
- Permissions not included in the update will be removed
- Existing permissions will be overwritten with the new values
- The admin address and manager splitter address remain unchanged

## Usage Example

```json
{
  "admin": "cosmos1abc123...",
  "address": "cosmos1managersplitter...",
  "permissions": {
    "canUpdateCollectionMetadata": {
      "approvedAddresses": [
        "cosmos1def456...",
        "cosmos1new789..."
      ]
    },
    "canUpdateTokenMetadata": {
      "approvedAddresses": [
        "cosmos1def456..."
      ]
    }
  }
}
```

In this example:
- The manager splitter's permissions are updated
- `cosmos1def456...` and `cosmos1new789...` can now update collection metadata
- Only `cosmos1def456...` can update token metadata
- All other permissions are removed (not set in the update)

## Important Notes

1. **Complete Replacement**: The permissions structure is completely replaced, not merged
2. **Admin Only**: Only the admin can update the manager splitter
3. **Admin Immutable**: The admin address cannot be changed via this message
4. **Address Immutable**: The manager splitter address cannot be changed
5. **Removal of Permissions**: Permissions not included in the update are effectively removed


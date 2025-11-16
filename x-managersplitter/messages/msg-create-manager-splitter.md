# MsgCreateManagerSplitter

## Overview

`MsgCreateManagerSplitter` creates a new Manager Splitter entity with specified permissions. The manager splitter receives a module-derived address that can be used as a collection manager.

## Message Structure

```protobuf
message MsgCreateManagerSplitter {
  option (cosmos.msg.v1.signer) = "admin";
  option (amino.name) = "managersplitter/CreateManagerSplitter";

  // Admin address creating the entity.
  string admin = 1;

  // Permissions mapping each CollectionPermission field to execution criteria.
  ManagerSplitterPermissions permissions = 2;
}
```

## Fields

### `admin` (string, required)
The address that will be the permanent admin of the manager splitter. This address:
- Has full control over the manager splitter
- Can always execute all permissions
- Can update and delete the manager splitter
- Cannot be changed after creation

Must be a valid Bech32 address.

### `permissions` (ManagerSplitterPermissions, optional)
The permissions configuration for the manager splitter. Each permission maps to `PermissionCriteria` that specifies which addresses can execute that permission.

If `nil` or not provided, an empty permissions structure is created (all permissions denied except for admin).

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

Each field is optional. If a permission is not set, it is denied by default (except for the admin).

## PermissionCriteria Structure

```protobuf
message PermissionCriteria {
  // List of approved addresses that can execute this permission.
  repeated string approvedAddresses = 1;
}
```

### `approvedAddresses` (string[], optional)
A list of Bech32 addresses that are approved to execute this permission. If empty, only the admin can execute this permission.

## Response

```protobuf
message MsgCreateManagerSplitterResponse {
  // The address of the created manager splitter.
  string address = 1;
}
```

### `address` (string)
The module-derived address of the created manager splitter. This address:
- Is deterministic based on the manager splitter ID
- Can be used as a collection manager address
- Is derived using `ModuleAddress(ModuleName, ID_bytes)`

## Validation

The message is validated to ensure:
1. The `admin` address is a valid Bech32 address
2. The manager splitter address doesn't already exist (shouldn't happen, but checked for safety)

## State Changes

1. A new Manager Splitter entity is created with:
   - A module-derived address (based on next available ID)
   - The specified admin address
   - The specified permissions (or empty if not provided)
2. The next manager splitter ID is incremented

## Usage Example

```json
{
  "admin": "cosmos1abc123...",
  "permissions": {
    "canUpdateCollectionMetadata": {
      "approvedAddresses": [
        "cosmos1def456...",
        "cosmos1ghi789..."
      ]
    },
    "canUpdateTokenMetadata": {
      "approvedAddresses": [
        "cosmos1def456..."
      ]
    },
    "canUpdateValidTokenIds": {
      "approvedAddresses": []
    }
  }
}
```

In this example:
- `cosmos1abc123...` is the admin with full control
- `cosmos1def456...` and `cosmos1ghi789...` can update collection metadata
- Only `cosmos1def456...` can update token metadata
- Only the admin can update valid token IDs (empty approved addresses list)
- All other permissions are denied (not set)

## Address Derivation

The manager splitter address is derived as:
```
address = ModuleAddress("managersplitter", ID_bytes)
```

Where `ID` is a sequential integer starting from 1. The address is deterministic and can be calculated if you know the ID.

## Important Notes

1. **Permanent Admin**: The admin address cannot be changed after creation
2. **Module Address**: The manager splitter address is a module address, not a regular account
3. **Empty Permissions**: If permissions are not provided, an empty structure is created (all permissions denied except admin)
4. **ID Increment**: The manager splitter ID is automatically incremented after creation


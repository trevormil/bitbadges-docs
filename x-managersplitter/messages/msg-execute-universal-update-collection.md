# MsgExecuteUniversalUpdateCollection

## Overview

`MsgExecuteUniversalUpdateCollection` allows an approved address (or admin) to execute a `UniversalUpdateCollection` message from the Badges module through a Manager Splitter. The message checks permissions before execution and uses the manager splitter address as the collection manager.

## Message Structure

```protobuf
message MsgExecuteUniversalUpdateCollection {
  option (cosmos.msg.v1.signer) = "executor";
  option (amino.name) = "managersplitter/ExecuteUniversalUpdateCollection";

  // Address executing the message (must be approved or admin).
  string executor = 1;

  // Address of the manager splitter to execute through.
  string managerSplitterAddress = 2;

  // The UniversalUpdateCollection message to execute.
  badges.MsgUniversalUpdateCollection universalUpdateCollectionMsg = 3;
}
```

## Fields

### `executor` (string, required)
The address that is executing the message. This address must be:
- The admin of the manager splitter, OR
- An approved address for all permissions required by the `UniversalUpdateCollection` message

Must be a valid Bech32 address.

### `managerSplitterAddress` (string, required)
The address of the manager splitter to execute through. This is the module-derived address returned when the manager splitter was created. Must be a valid Bech32 address.

### `universalUpdateCollectionMsg` (badges.MsgUniversalUpdateCollection, required)
The `UniversalUpdateCollection` message from the Badges module to execute. This message will be executed with the manager splitter address as the creator/manager.

## Permission Checking

The module checks permissions based on which fields are being updated in the `UniversalUpdateCollection` message:

- **UpdateValidTokenIds**: Requires `canUpdateValidTokenIds` permission
- **UpdateCollectionPermissions**: Requires `canUpdateCollectionApprovals` permission
- **UpdateManagerTimeline**: Requires `canUpdateManager` permission
- **UpdateCollectionMetadataTimeline**: Requires `canUpdateCollectionMetadata` permission
- **UpdateTokenMetadataTimeline**: Requires `canUpdateTokenMetadata` permission
- **UpdateCustomDataTimeline**: Requires `canUpdateCustomData` permission
- **UpdateCollectionApprovals**: Requires `canUpdateCollectionApprovals` permission
- **UpdateStandardsTimeline**: Requires `canUpdateStandards` permission
- **UpdateIsArchivedTimeline**: Requires `canArchiveCollection` permission

**Important**: All required permissions are checked. If the executor is not approved for any required permission (and is not the admin), the transaction will fail.

## Response

```protobuf
message MsgExecuteUniversalUpdateCollectionResponse {
  // ID of the collection that was updated.
  string collectionId = 1 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
}
```

### `collectionId` (Uint)
The ID of the collection that was updated, returned from the Badges module's `UniversalUpdateCollection` execution.

## Validation

The message is validated to ensure:
1. The `executor` address is a valid Bech32 address
2. The `managerSplitterAddress` is a valid Bech32 address
3. The manager splitter exists
4. The executor has permission to execute all required actions
5. The `universalUpdateCollectionMsg` is valid

## Authorization

The executor must be:
- The admin of the manager splitter (has full permissions), OR
- An approved address for all permissions required by the update

If the executor doesn't have the required permissions, the transaction will fail with a permission denied error.

## Execution Flow

1. **Permission Check**: The module checks if the executor has permission for all required actions
2. **Message Preparation**: The `UniversalUpdateCollection` message is prepared with the manager splitter address as the creator
3. **Badges Module Execution**: The message is forwarded to the Badges module's `UniversalUpdateCollection` handler
4. **Response**: The collection ID is returned

## Important Notes

### Manager Splitter as Manager

The manager splitter address is used as the `creator` field in the `UniversalUpdateCollection` message. This means:
- The Badges module sees the manager splitter address as the manager
- The collection's manager must be set to the manager splitter address
- The executor address is not visible to the Badges module

### Admin Override

The admin address always has full permissions and can execute any `UniversalUpdateCollection` message through the manager splitter, regardless of the permission settings.

### Atomic Execution

Permission checks and message execution are atomic:
- If permissions are denied, the transaction fails before any state changes
- If the Badges module execution fails, the entire transaction is rolled back

## Usage Example

```json
{
  "executor": "cosmos1def456...",
  "managerSplitterAddress": "cosmos1managersplitter...",
  "universalUpdateCollectionMsg": {
    "creator": "cosmos1managersplitter...",
    "collectionId": "1",
    "updateCollectionMetadataTimeline": true,
    "collectionMetadataTimeline": {
      "timelineTimes": [
        {
          "timelineTime": "2024-01-01T00:00:00Z",
          "collectionMetadata": {
            "name": "Updated Collection Name"
          }
        }
      ]
    }
  }
}
```

In this example:
- `cosmos1def456...` is executing the update
- The manager splitter address is `cosmos1managersplitter...`
- The update modifies collection metadata
- The executor must have `canUpdateCollectionMetadata` permission (or be the admin)

## Error Cases

1. **Invalid Executor**: Executor address is not valid Bech32
2. **Manager Splitter Not Found**: The manager splitter address doesn't exist
3. **Permission Denied**: Executor doesn't have required permissions
4. **Invalid Message**: The `UniversalUpdateCollection` message is invalid
5. **Badges Module Error**: The Badges module execution fails


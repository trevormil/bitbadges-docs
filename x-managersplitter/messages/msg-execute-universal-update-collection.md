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

-   The admin of the manager splitter, OR
-   An approved address for all permissions required by the `UniversalUpdateCollection` message

Must be a valid Bech32 address.

### `managerSplitterAddress` (string, required)

The address of the manager splitter to execute through. This is the module-derived address returned when the manager splitter was created. Must be a valid Bech32 address.

### `universalUpdateCollectionMsg` (badges.MsgUniversalUpdateCollection, required)

The `UniversalUpdateCollection` message from the Badges module to execute. This message will be executed with the manager splitter address as the creator/manager.

Note that you should only set the fields you are allowed to update. Use the update flag approach = false for all others.

## Permission Checking

The module checks permissions based on which fields are being updated in the `UniversalUpdateCollection` message:

-   **UpdateValidTokenIds**: Requires `canUpdateValidTokenIds` permission
-   **UpdateCollectionPermissions**: Requires `canUpdateCollectionApprovals` permission
-   **UpdateManager**: Requires `canUpdateManager` permission
-   **UpdateCollectionMetadata**: Requires `canUpdateCollectionMetadata` permission
-   **UpdateTokenMetadata**: Requires `canUpdateTokenMetadata` permission
-   **UpdateCustomData**: Requires `canUpdateCustomData` permission
-   **UpdateCollectionApprovals**: Requires `canUpdateCollectionApprovals` permission
-   **UpdateStandards**: Requires `canUpdateStandards` permission
-   **UpdateIsArchived**: Requires `canArchiveCollection` permission
-   **CosmosCoinWrapperPathsToAdd**: Requires `canAddMoreCosmosCoinWrapperPaths` permission
-   **AliasPathsToAdd**: Requires `canAddMoreAliasPaths` permission

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

-   The admin of the manager splitter (has full permissions), OR
-   An approved address for all permissions required by the update

If the executor doesn't have the required permissions, the transaction will fail with a permission denied error.

## Usage Example

```json
{
  "executor": "bb1...",
  "managerSplitterAddress": "bb1managersplitter...",
  "universalUpdateCollectionMsg": {
    "creator": "bb1managersplitter...",
    "collectionId": "1",
    ...
    "updateCollectionMetadata": true,
    "collectionMetadata": {...}
  }
}
```

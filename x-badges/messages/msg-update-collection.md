# MsgUpdateCollection

Updates an existing collection's properties.

## Update Flag Pattern

This message uses an update flag + value pattern for selective updates. Each updatable field has a corresponding boolean flag (e.g., `updateValidTokenIds`, `updateCollectionPermissions`).

-   **If update flag is `true`**: The corresponding value field is processed and the collection is updated with the new value
-   **If update flag is `false`**: The corresponding value field is completely ignored, regardless of what data is provided

This allows you to update only specific fields without affecting others, and you can safely leave unused value fields empty or with placeholder data.

## Authorization & Permissions

Updates can only be performed by the **current manager** of the collection. All updates must obey the previously set permissions - meaning the permission settings that were in effect _before_ this message was started.

**Important**: If you update the permissions in the current message, those new permissions are applied last and will not be applicable until the following transaction. This prevents circumventing permission restrictions within the same transaction.

## Proto Definition

```protobuf
message MsgUpdateCollection {
  string creator = 1; // Address updating collection (must be manager)
  string collectionId = 2; // ID of collection to update
  bool updateValidTokenIds = 3;
  repeated UintRange validTokenIds = 4;
  bool updateCollectionPermissions = 5;
  CollectionPermissions collectionPermissions = 6;
  bool updateManagerTimeline = 7;
  repeated ManagerTimeline managerTimeline = 8;
  bool updateCollectionMetadataTimeline = 9;
  repeated CollectionMetadataTimeline collectionMetadataTimeline = 10;
  bool updateTokenMetadataTimeline = 11;
  repeated TokenMetadataTimeline tokenMetadataTimeline = 12;
  bool updateCustomDataTimeline = 13;
  repeated CustomDataTimeline customDataTimeline = 14;
  bool updateCollectionApprovals = 15;
  repeated CollectionApproval collectionApprovals = 16;
  bool updateStandardsTimeline = 17;
  repeated StandardsTimeline standardsTimeline = 18;
  bool updateIsArchivedTimeline = 19;
  repeated IsArchivedTimeline isArchivedTimeline = 20;
  repeated cosmos.base.v1beta1.Coin mintEscrowCoinsToTransfer = 21;
  repeated CosmosCoinWrapperPathAddObject cosmosCoinWrapperPathsToAdd = 22;
  repeated AliasPathAddObject aliasPathsToAdd = 23;
  CollectionInvariants invariants = 24;
}

message MsgUpdateCollectionResponse {
  string collectionId = 1; // ID of updated collection
}
```

## Usage Example

```bash
# CLI command
bitbadgeschaind tx badges update-collection '[tx-json]' --from manager-key
```

### JSON Example

```json
{
    "creator": "bb1abc123...",
    "collectionId": "1",
    "updateValidTokenIds": true,
    "validTokenIds": [{ "start": "1", "end": "200" }],
    "updateCollectionPermissions": false,
    "collectionPermissions": {
        "canDeleteCollection": [],
        "canArchiveCollection": [],
        "canUpdateStandards": [],
        "canUpdateCustomData": [],
        "canUpdateManager": [],
        "canUpdateCollectionMetadata": [],
        "canUpdateValidTokenIds": [],
        "canUpdateTokenMetadata": [],
        "canUpdateCollectionApprovals": [],
        "canAddMoreAliasPaths": [],
        "canAddMoreCosmosCoinWrapperPaths": []
    },
    "updateManagerTimeline": false,
    "managerTimeline": [],
    "updateCollectionMetadataTimeline": false,
    "collectionMetadataTimeline": [],
    "updateTokenMetadataTimeline": false,
    "tokenMetadataTimeline": [],
    "updateCustomDataTimeline": false,
    "customDataTimeline": [],
    "updateCollectionApprovals": false,
    "collectionApprovals": [],
    "updateStandardsTimeline": false,
    "standardsTimeline": [],
    "updateIsArchivedTimeline": false,
    "isArchivedTimeline": [],
    "mintEscrowCoinsToTransfer": [],
    "cosmosCoinWrapperPathsToAdd": [],
    "aliasPathsToAdd": [],
    "invariants": {
        "noCustomOwnershipTimes": false,
        "maxSupplyPerId": "0",
        "cosmosCoinBackedPath": undefined,
        "noForcefulPostMintTransfers": false,
        "disablePoolCreation": false
    }
}
```

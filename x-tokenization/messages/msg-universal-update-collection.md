# MsgUniversalUpdateCollection

A universal message that can be used to either create a new collection or update an existing one. This message combines the functionality of both `MsgCreateCollection` and `MsgUpdateCollection` into a single interface.

## Dual Purpose

-   **Collection Creation**: When `collectionId` is set to `"0"`, this message creates a new collection
-   **Collection Update**: When `collectionId` is set to an existing collection ID, this message updates that collection

## Update Flag Pattern

This message uses an update flag + value pattern for selective updates. Each updatable field has a corresponding boolean flag (e.g., `updateValidTokenIds`, `updateCollectionPermissions`).

-   **If update flag is `true`**: The corresponding value field is processed and the collection is updated with the new value
-   **If update flag is `false`**: The corresponding value field is completely ignored, regardless of what data is provided

## Authorization & Permissions

-   **For Collection Creation**: Can be executed by any address
-   **For Collection Updates**: Can only be executed by the **current manager** of the collection. All updates must obey the previously set permissions.

### Path Addition Permissions

When adding paths to an existing collection, the following permissions are checked:

-   **`cosmosCoinWrapperPathsToAdd`**: Requires `canAddMoreCosmosCoinWrapperPaths` permission
-   **`aliasPathsToAdd`**: Requires `canAddMoreAliasPaths` permission

These permissions are checked before paths are processed. If the permission check fails, the transaction will be rejected. Both permissions use the `ActionPermission` type with time-based controls. Empty/nil permissions mean the action is allowed (neutral state).

## Proto Definition

```protobuf
message MsgUniversalUpdateCollection {
  string creator = 1; // Address creating/updating collection
  string collectionId = 2; // "0" for new collection, existing ID for updates

  // Creation-only fields (only used when collectionId = "0")
  UserBalanceStore defaultBalances = 3;

  // Updateable fields (used for both creation and updates)
  repeated UintRange validTokenIds = 4;
  bool updateCollectionPermissions = 5;
  CollectionPermissions collectionPermissions = 6;
  bool updateManager = 7;
  string manager = 8;
  bool updateCollectionMetadata = 9;
  CollectionMetadata collectionMetadata = 10;
  bool updateTokenMetadata = 11;
  repeated TokenMetadata tokenMetadata = 12;
  bool updateCustomData = 13;
  string customData = 14;
  bool updateCollectionApprovals = 15;
  repeated CollectionApproval collectionApprovals = 16;
  bool updateStandards = 17;
  repeated string standards = 18;
  bool updateIsArchived = 19;
  bool isArchived = 20;

  // Transfer fields
  repeated cosmos.base.v1beta1.Coin mintEscrowCoinsToTransfer = 21;
  repeated CosmosCoinWrapperPathAddObject cosmosCoinWrapperPathsToAdd = 22; // Requires canAddMoreCosmosCoinWrapperPaths permission
  repeated AliasPathAddObject aliasPathsToAdd = 23; // Requires canAddMoreAliasPaths permission

  // Invariants (creation-only)
  CollectionInvariants invariants = 24;
}

message MsgUniversalUpdateCollectionResponse {
  string collectionId = 1; // ID of created/updated collection
}
```

## Usage Example

```bash
# CLI command
bitbadgeschaind tx tokenization universal-update-collection '[tx-json]' --from creator-key
```

### JSON Example - Creating a New Collection

```json
{
    "creator": "bb1abc123...",
    "collectionId": "0",
    "defaultBalances": {
        "balances": [],
        "outgoingApprovals": [],
        "incomingApprovals": [],
        "autoApproveSelfInitiatedOutgoingTransfers": false,
        "autoApproveSelfInitiatedIncomingTransfers": true,
        "autoApproveAllIncomingTransfers": false,
        "userPermissions": {
            "canUpdateOutgoingApprovals": [],
            "canUpdateIncomingApprovals": [],
            "canUpdateAutoApproveSelfInitiatedOutgoingTransfers": [],
            "canUpdateAutoApproveSelfInitiatedIncomingTransfers": [],
            "canUpdateAutoApproveAllIncomingTransfers": []
        }
    },
    "validTokenIds": [{ "start": "1", "end": "100" }],
    "updateCollectionPermissions": true,
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
    "updateManager": true,
    "manager": "",
    "updateCollectionMetadata": true,
    "collectionMetadata": {},
    "updateTokenMetadata": true,
    "tokenMetadata": [],
    "updateCustomData": true,
    "customData": "",
    "updateCollectionApprovals": true,
    "collectionApprovals": [],
    "updateStandards": true,
    "standards": [],
    "updateIsArchived": true,
    "isArchived": false,
    "mintEscrowCoinsToTransfer": [],
    "cosmosCoinWrapperPathsToAdd": [],
    "aliasPathsToAdd": [],
    "invariants": {
        "noCustomOwnershipTimes": false,
        "maxSupplyPerId": "0",
        "cosmosCoinBackedPath": undefined,
        "noForcefulPostMintTransfers": false,
        "disablePoolCreation": false,
        "evmQueryChallenges": []
    }
}
```

### JSON Example - Updating an Existing Collection

```json
{
    "creator": "bb1abc123...",
    "collectionId": "1",
    "updateValidTokenIds": true,
    "validTokenIds": [{ "start": "1", "end": "200" }],
    "updateCollectionPermissions": false,
    "collectionPermissions": {},
    "updateManager": false,
    "manager": "",
    "updateCollectionMetadata": false,
    "collectionMetadata": {},
    "updateTokenMetadata": false,
    "tokenMetadata": [],
    "updateCustomData": false,
    "customData": "",
    "updateCollectionApprovals": false,
    "collectionApprovals": [],
    "updateStandards": false,
    "standards": [],
    "updateIsArchived": false,
    "isArchived": false,
    "mintEscrowCoinsToTransfer": [],
    "cosmosCoinWrapperPathsToAdd": [],
    "aliasPathsToAdd": [],
    "invariants": {}
}
```

## Key Differences from Other Messages

### vs MsgCreateCollection

-   More flexible update flag pattern
-   Can be used for both creation and updates
-   Includes invariants support

### vs MsgUpdateCollection

-   Can create new collections when collectionId = "0"
    -   Includes creation-only fields like `defaultBalances`
-   Includes invariants support

## Invariants Support

When creating a new collection (collectionId = "0"), you can set collection invariants using the `invariants` field. Invariants cannot be modified after collection creation.

```json
{
    "invariants": {
        "noCustomOwnershipTimes": true,
        "maxSupplyPerId": "0",
        "cosmosCoinBackedPath": undefined,
        "noForcefulPostMintTransfers": false,
        "disablePoolCreation": false,
        "evmQueryChallenges": []
    }
}
```

## Related Messages

-   [MsgCreateCollection](msg-create-collection.md)
-   [MsgUpdateCollection](msg-update-collection.md)
-   [Collection Setup Fields](../../token-standard/learn/collection-setup-fields.md)

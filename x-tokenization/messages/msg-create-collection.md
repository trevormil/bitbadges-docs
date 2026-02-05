# MsgCreateCollection

Creates a new collection.

The collectionId will be assigned at execution time and is obtainable in the transaction response. Subsequent updates to the collection will be through MsgUpdateCollection.

## Creation Only Properties

The creation or genesis transaction for a collection is unique in a couple ways.

There are no permissions previously set, so there are no restrictions for what can be set vs not. Subsequent updates to the collection must follow any previously set permissions.

This is the only time that you can specify the `defaultBalances` information.

## Proto Definition

```protobuf
message MsgCreateCollection {
  string creator = 1; // Address creating the collection
  UserBalanceStore defaultBalances = 2;
  repeated UintRange validTokenIds  = 3; // Token ID ranges to include
  CollectionPermissions collectionPermissions = 4;
  string manager = 5;
  CollectionMetadata collectionMetadata = 6;
  repeated TokenMetadata tokenMetadata = 7;
  string customData = 8;
  repeated CollectionApproval collectionApprovals = 9;
  repeated string standards = 10;
  bool isArchived = 11;
  repeated cosmos.base.v1beta1.Coin mintEscrowCoinsToTransfer = 12;
  repeated CosmosCoinWrapperPathAddObject cosmosCoinWrapperPathsToAdd = 13;
  repeated AliasPathAddObject aliasPathsToAdd = 14; // NEW: Separate array for alias paths
  CollectionInvariants invariants = 15;
}

message MsgCreateCollectionResponse {
  string collectionId = 1; // ID of the created collection
}
```

## Usage Example

```bash
# CLI command
bitbadgeschaind tx tokenization create-collection '[tx-json]' --from creator-key
```

### JSON Example

For complete transaction examples, see [MsgCreateCollection Examples](../examples/txs/msgcreatecollection/).

```json
{
    "creator": "bb1...",
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
    "manager": "",
    "collectionMetadata": {},
    "tokenMetadata": [],
    "customData": "",
    "collectionApprovals": [],
    "standards": [],
    "isArchived": false,
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

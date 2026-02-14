# MsgSetCollectionMetadata

**Disclaimer:**\
This message is a streamlined alternative to [MsgUpdateCollection](broken-reference). If you need to update many fields at once, we recommend using MsgUpdateCollection instead.

## MsgSetCollectionMetadata

Sets the collection metadata and update permissions for a collection. This is a convenience message that focuses specifically on collection metadata management.

### Overview

This message allows you to:

-   Set collection metadata for the collection
-   Configure permissions to update the collection metadata in the future

### Authorization & Permissions

Updates can only be performed by the **current manager** of the collection. The manager must have permission to update the collection metadata according to the collection's current permission settings.

### Proto Definition

```protobuf
message MsgSetCollectionMetadata {
  option (cosmos.msg.v1.signer) = "creator";
  option (amino.name) = "tokenization/SetCollectionMetadata";

  // Address of the creator.
  string creator = 1;

  // ID of the collection.
  string collectionId = 2 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];

  // New collection metadata to set.
  CollectionMetadata collectionMetadata = 3;

  // Permission to update collection metadata
  repeated ActionPermission canUpdateCollectionMetadata = 4;
}

message MsgSetCollectionMetadataResponse {
  // ID of the collection.
  string collectionId = 1 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
}
```

### Usage Example

```bash
# CLI command
bitbadgeschaind tx tokenization set-collection-metadata '[tx-json]' --from manager-key
```

#### JSON Example

```json
{
    "creator": "bb1abc123...",
    "collectionId": "1",
    "collectionMetadata": {
        "uri": "https://example.com/collection.json",
        "customData": "{\"description\": \"My collection\"}"
    },
    "canUpdateCollectionMetadata": [
        {
            "permanentlyPermittedTimes": [{ "start": "1000", "end": "2000" }],
            "permanentlyForbiddenTimes": []
        }
    ]
}
```

### Related Messages

-   [MsgUniversalUpdateCollection](broken-reference) - Full collection update with all fields
-   [MsgUpdateCollection](broken-reference) - Legacy update message

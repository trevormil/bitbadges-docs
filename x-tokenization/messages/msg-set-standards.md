# MsgSetStandards

**Disclaimer:**\
This message is a streamlined alternative to [MsgUpdateCollection](broken-reference). If you need to update many fields at once, we recommend using MsgUpdateCollection instead.

## MsgSetStandards

Sets the standards and update permissions for a collection. This is a convenience message that focuses specifically on standards management.

### Overview

This message allows you to:

-   Set standards for the collection
-   Configure permissions to update the standards in the future

### Authorization & Permissions

Updates can only be performed by the **current manager** of the collection. The manager must have permission to update the standards according to the collection's current permission settings.

### Proto Definition

```protobuf
message MsgSetStandards {
  option (cosmos.msg.v1.signer) = "creator";
  option (amino.name) = "tokenization/SetStandards";

  // Address of the creator.
  string creator = 1;

  // ID of the collection.
  string collectionId = 2 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];

  // New standards to set.
  repeated string standards = 3;

  // Permission to update standards
  repeated ActionPermission canUpdateStandards = 4;
}

message MsgSetStandardsResponse {
  // ID of the collection.
  string collectionId = 1 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
}
```

### Usage Example

```bash
# CLI command
bitbadgeschaind tx tokenization set-standards '[tx-json]' --from manager-key
```

#### JSON Example

```json
{
    "creator": "bb1abc123...",
    "collectionId": "1",
    "standards": ["ERC1155", "ERC721"],
    "canUpdateStandards": [
        {
            "permanentlyPermittedTimes": [{ "start": "1000", "end": "2000" }],
            "permanentlyForbiddenTimes": []
        }
    ]
}
```

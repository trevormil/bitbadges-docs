# MsgSetManager

**Disclaimer:**\
This message is a streamlined alternative to [MsgUpdateCollection](broken-reference). If you need to update many fields at once, we recommend using MsgUpdateCollection instead.

## MsgSetManager

Sets the manager and update permissions for a collection. This is a convenience message that focuses specifically on manager management.

### Overview

This message allows you to:

-   Set who manages the collection
-   Configure permissions to update the manager in the future

### Authorization & Permissions

Updates can only be performed by the **current manager** of the collection. The manager must have permission to update the manager according to the collection's current permission settings.

### Proto Definition

```protobuf
message MsgSetManager {
  option (cosmos.msg.v1.signer) = "creator";
  option (amino.name) = "tokenization/SetManager";

  // Address of the creator.
  string creator = 1;

  // ID of the collection.
  string collectionId = 2 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];

  // New manager to set.
  string manager = 3;

  // Permission to update manager
  repeated ActionPermission canUpdateManager = 4;
}

message MsgSetManagerResponse {
  // ID of the collection.
  string collectionId = 1 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
}
```

### Usage Example

```bash
# CLI command
bitbadgeschaind tx tokenization set-manager '[tx-json]' --from manager-key
```

#### JSON Example

```json
{
    "creator": "bb1abc123...",
    "collectionId": "1",
    "manager": "bb1def456...",
    "canUpdateManager": [
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

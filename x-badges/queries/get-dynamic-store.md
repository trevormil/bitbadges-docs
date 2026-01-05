# GetDynamicStore

Retrieves information about a dynamic store.

## Proto Definition

```protobuf
message QueryGetDynamicStoreRequest {
  string storeId = 1;
}

message QueryGetDynamicStoreResponse {
  DynamicStore store = 1;
}

message DynamicStore {
  // The unique identifier for this dynamic store. This is assigned by the blockchain.
  string storeId = 1 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
  // The address of the creator of this dynamic store.
  string createdBy = 2;
  // The default value for uninitialized addresses.
  bool defaultValue = 3;
  // Global kill switch. When false, all approvals using this store fail immediately.
  bool globalEnabled = 4;
  // URI for additional metadata or resources associated with this dynamic store.
  string uri = 5;
  // Custom data field for storing arbitrary data associated with this dynamic store.
  string customData = 6;
}
```

## Usage Example

```bash
# CLI query
bitbadgeschaind query badges get-dynamic-store [store-id]

# REST API
curl "https://lcd.bitbadges.io/bitbadges/bitbadgeschain/badges/get_dynamic_store/1"
```

### Response Example

```json
{
    "store": {
        "storeId": "1",
        "createdBy": "bb1...",
        "defaultValue": false,
        "globalEnabled": true,
        "uri": "https://example.com/metadata",
        "customData": "{\"key\": \"value\"}"
    }
}
```

**Note**: Both `uri` and `customData` are optional fields. They may be empty strings (`""`) if not set when creating or updating the store.

## Global Kill Switch

The `globalEnabled` field indicates whether the global kill switch is enabled for this store. When `globalEnabled = false`, all approvals using this store via `DynamicStoreChallenge` will fail immediately, regardless of per-address values.

-   **New stores**: Default to `globalEnabled = true`
-   **Existing stores**: Set to `globalEnabled = true` for backward compatibility
-   **Disabling**: Use [MsgUpdateDynamicStore](../messages/msg-update-dynamic-store.md) to set `globalEnabled = false`

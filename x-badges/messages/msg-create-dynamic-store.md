# MsgCreateDynamicStore

Creates a new dynamic store for boolean key-value storage. New stores are created with `globalEnabled = true` by default, meaning the store is active and can be used in approval criteria.

## Proto Definition

```protobuf
message MsgCreateDynamicStore {
  string creator = 1; // Address creating the dynamic store
  bool defaultValue = 2; // Default boolean value for uninitialized addresses
  string uri = 3; // Optional: URI for additional metadata or resources
  string customData = 4; // Optional: Custom data field for arbitrary data
}

message MsgCreateDynamicStoreResponse {
  string storeId = 1; // ID of the created dynamic store
}
```

## Usage Example

```bash
# CLI command
bitbadgeschaind tx badges create-dynamic-store [true|false] --from creator-key
```

### JSON Examples

**Basic example (without metadata):**
```json
{
  "creator": "bb1...",
  "defaultValue": false
}
```

**With optional metadata fields:**
```json
{
  "creator": "bb1...",
  "defaultValue": false,
  "uri": "https://example.com/store-metadata",
  "customData": "{\"description\": \"Member store\", \"version\": \"1.0\"}"
}
```

## Metadata Fields

Both `uri` and `customData` are **optional** fields:

- **`uri`**: URI for additional metadata or resources associated with this dynamic store. Typically used for URLs pointing to hosted JSON metadata or documentation. No validation is performed on the URI format.
- **`customData`**: Custom data field for storing arbitrary string data. Commonly used to store JSON-encoded structured data, but can contain any string value. No validation is performed on the content.

**Important Notes:**
- Both fields default to empty strings (`""`) if not provided
- Fields are stored and persisted with the DynamicStore
- Fields can be updated later using [MsgUpdateDynamicStore](msg-update-dynamic-store.md)
- Fields are included in EIP712 schemas for Ethereum signature compatibility
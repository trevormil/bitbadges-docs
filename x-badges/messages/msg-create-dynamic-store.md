# MsgCreateDynamicStore

Creates a new dynamic store for boolean key-value storage. New stores are created with `globalEnabled = true` by default, meaning the store is active and can be used in approval criteria.

## Proto Definition

```protobuf
message MsgCreateDynamicStore {
  string creator = 1; // Address creating the dynamic store
  bool defaultValue = 2; // Default boolean value for uninitialized addresses
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

### JSON Example
```json
{
  "creator": "bb1...",
  "defaultValue": false
}
```
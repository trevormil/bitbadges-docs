# MsgSetDynamicStoreValue

Sets a boolean value for a specific address in a dynamic store.

## Proto Definition

```protobuf
message MsgSetDynamicStoreValue {
  string creator = 1; // Address setting the value (must be store creator)
  string storeId = 2; // ID of the dynamic store
  string address = 3; // Address to set the value for
  bool value = 4; // Boolean value to set
}

message MsgSetDynamicStoreValueResponse {
  bool previousValue = 1; // The previous value before the update
  repeated string reviewItems = 2; // Advisory review items about the transaction
}
```

## Response

The response includes:

-   **`previousValue`**: The boolean value that was stored before this update
-   **`reviewItems`**: Advisory strings about the transaction (see [Review Items](../concepts/approval-change-events.md#review-items))

## Usage Example

```bash
# CLI command
bitbadgeschaind tx tokenization set-dynamic-store-value [store-id] [address] [true|false] --from creator-key
```

### JSON Example
```json
{
  "creator": "bb1...",
  "storeId": "1",
  "address": "bb1...",
  "value": true
}
```
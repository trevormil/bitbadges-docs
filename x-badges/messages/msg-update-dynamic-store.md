# MsgUpdateDynamicStore

Updates an existing dynamic store's default value and global kill switch status.

## Proto Definition

```protobuf
message MsgUpdateDynamicStore {
  string creator = 1; // Address updating the store (must be creator)
  string storeId = 2; // ID of dynamic store to update
  bool defaultValue = 3; // New default value for uninitialized addresses
  bool globalEnabled = 4; // Global kill switch. When false, all approvals using this store fail immediately
}

message MsgUpdateDynamicStoreResponse {}
```

## Usage Example

```bash
# CLI command
bitbadgeschaind tx badges update-dynamic-store [store-id] [default-value] [global-enabled] --from creator-key
```

### JSON Examples

**Update default value only (keep globalEnabled unchanged):**
```json
{
    "creator": "bb1...",
    "storeId": "1",
    "defaultValue": true,
    "globalEnabled": true  // Must pass current value to avoid changing it
}
```

**Disable global kill switch (halt all approvals using this store):**
```json
{
    "creator": "bb1...",
    "storeId": "1",
    "defaultValue": true,  // Must pass current value
    "globalEnabled": false  // Disable kill switch
}
```

**Re-enable global kill switch:**
```json
{
    "creator": "bb1...",
    "storeId": "1",
    "defaultValue": true,
    "globalEnabled": true  // Re-enable
}
```

## Global Kill Switch

The `globalEnabled` field acts as a global kill switch for the dynamic store. When `globalEnabled = false`:
- All approvals using this store via `DynamicStoreChallenge` will fail immediately
- The error message will be: "dynamic store storeId {id} is globally disabled"
- Per-address values are ignored when the kill switch is disabled

This is useful for quickly halting all approvals that depend on a specific dynamic store (e.g., if a protocol is compromised).

**Note**: When updating a store, you must pass the current `globalEnabled` value if you want to keep it unchanged. Proto3 bools default to `false`, so you must explicitly pass `true` to maintain an enabled state.

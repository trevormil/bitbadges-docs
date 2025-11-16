# MsgDeleteManagerSplitter

## Overview

`MsgDeleteManagerSplitter` deletes an existing Manager Splitter entity. Only the admin address can delete the manager splitter.

## Message Structure

```protobuf
message MsgDeleteManagerSplitter {
  option (cosmos.msg.v1.signer) = "admin";
  option (amino.name) = "managersplitter/DeleteManagerSplitter";

  // Admin address deleting the entity.
  string admin = 1;

  // Address of the manager splitter to delete.
  string address = 2;
}
```

## Fields

### `admin` (string, required)
The admin address of the manager splitter. This must match the manager splitter's stored admin address. Must be a valid Bech32 address.

### `address` (string, required)
The address of the manager splitter to delete. This is the module-derived address returned when the manager splitter was created. Must be a valid Bech32 address.

## Response

```protobuf
message MsgDeleteManagerSplitterResponse {}
```

An empty response indicates successful deletion.

## Validation

The message is validated to ensure:
1. The `admin` address is a valid Bech32 address
2. The `address` is a valid Bech32 address
3. The manager splitter exists
4. The `admin` matches the manager splitter's stored admin address

## Authorization

Only the admin address can delete the manager splitter. If the `admin` field doesn't match the manager splitter's stored admin, the transaction will fail with an unauthorized error.

## State Changes

The manager splitter entity is completely removed from state. This means:
- The manager splitter record is deleted
- All permission configurations are removed
- The manager splitter address can no longer be used as a collection manager (unless it was already set)

## Important Considerations

### Collections Using the Manager Splitter

If a badge collection has the manager splitter address set as its manager:
- The manager splitter address will still be valid in the badges module
- However, no one will be able to execute actions through the manager splitter (since it no longer exists)
- The collection's manager would need to be updated to a different address

### Irreversible Action

Deletion is permanent and cannot be undone. If you need to recreate a manager splitter:
- It will receive a new ID and address
- You would need to update any collections using the old address

## Usage Example

```json
{
  "admin": "cosmos1abc123...",
  "address": "cosmos1managersplitter..."
}
```

## Important Notes

1. **Admin Only**: Only the admin can delete the manager splitter
2. **Permanent**: Deletion is permanent and cannot be undone
3. **Collections Impact**: Collections using the manager splitter address will need their manager updated
4. **No Validation**: The module does not check if any collections are using the manager splitter before deletion


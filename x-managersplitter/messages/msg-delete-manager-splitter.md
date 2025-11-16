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

## Authorization

Only the admin address can delete the manager splitter. If the `admin` field doesn't match the manager splitter's stored admin, the transaction will fail with an unauthorized error.

## Usage Example

```json
{
  "admin": "cosmos1abc123...",
  "address": "cosmos1managersplitter..."
}
```

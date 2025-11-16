# MsgUpdateParams

## Overview

`MsgUpdateParams` is the governance message used to update the Manager Splitter module's parameters.

## Message Structure

```protobuf
message MsgUpdateParams {
  option (cosmos.msg.v1.signer) = "authority";
  option (amino.name) = "managersplitter/MsgUpdateParams";

  // authority is the address that controls the module (defaults to x/gov unless overwritten).
  string authority = 1 [(cosmos_proto.scalar) = "cosmos.AddressString"];

  // params defines the module parameters to update.
  //
  // NOTE: All parameters must be supplied.
  Params params = 2 [
    (gogoproto.nullable) = false,
    (amino.dont_omitempty) = true
  ];
}
```

## Fields

### `authority` (string, required)
The address authorized to update the module parameters. This must match the module's configured authority address (defaults to the governance module account).

### `params` (Params, required)
The complete set of module parameters to set. All parameters must be supplied - partial updates are not supported.

## Validation

The message is validated to ensure:
1. The `authority` address matches the module's configured authority
2. All parameters are valid
3. All required fields are provided

## Response

```protobuf
message MsgUpdateParamsResponse {}
```

An empty response indicates successful parameter update.

## Governance

This message is typically submitted as a governance proposal, allowing the community to vote on parameter changes. The authority address must sign the transaction.


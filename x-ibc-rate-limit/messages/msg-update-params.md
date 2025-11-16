# MsgUpdateParams

## Overview

`MsgUpdateParams` is the governance message used to update the IBC Rate Limit module's parameters. This includes all rate limit configurations for channels and denominations.

## Message Structure

```protobuf
message MsgUpdateParams {
  option (cosmos.msg.v1.signer) = "authority";
  option (amino.name) = "ibcratelimit/MsgUpdateParams";

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

The `Params` structure contains:
- `rate_limits`: An array of `RateLimitConfig` objects, each defining rate limits for a specific channel and denomination combination

## RateLimitConfig Structure

Each rate limit configuration specifies:

```protobuf
message RateLimitConfig {
  // channel_id is the IBC channel ID this rate limit applies to
  // If empty, applies to all channels
  string channel_id = 1;

  // denom is the denomination this rate limit applies to
  // Must be specified (empty denoms are not allowed)
  string denom = 2;

  // supply_shift_limits defines multiple timeframe limits for supply shift
  repeated TimeframeLimit supply_shift_limits = 5;

  // unique_sender_limits defines limits on unique senders per channel
  repeated UniqueSenderLimit unique_sender_limits = 6;

  // address_limits defines per-address transfer limits
  repeated AddressLimit address_limits = 7;
}
```

### Configuration Matching

Configurations are checked in order, and the **first matching config** is used. A config matches if:
- The `channel_id` matches the transfer's channel (or is empty to match all channels)
- The `denom` matches the transfer's denomination

If no config matches, the transfer is allowed (no rate limit applied).

## TimeframeLimit Structure

Defines limits on supply shift (net flow):

```protobuf
message TimeframeLimit {
  // max_amount is the maximum absolute amount of supply change allowed
  // If set to 0, this limit is disabled
  string max_amount = 1 [(gogoproto.customtype) = "cosmossdk.io/math.Int"];

  // timeframe_type defines the type of timeframe (block, hour, day)
  TimeframeType timeframe_type = 2;

  // timeframe_duration is the duration of the timeframe
  int64 timeframe_duration = 3;
}
```

## UniqueSenderLimit Structure

Defines limits on the number of unique sender addresses:

```protobuf
message UniqueSenderLimit {
  // max_unique_senders is the maximum number of unique senders allowed
  // If set to 0, this limit is disabled
  int64 max_unique_senders = 1;

  // timeframe_type defines the type of timeframe
  TimeframeType timeframe_type = 2;

  // timeframe_duration is the duration of the timeframe
  int64 timeframe_duration = 3;
}
```

## AddressLimit Structure

Defines per-address transfer limits:

```protobuf
message AddressLimit {
  // max_transfers is the maximum number of transfers allowed per address
  // If set to 0, transfer count limit is disabled
  int64 max_transfers = 1;

  // max_amount is the maximum total amount allowed per address
  // If set to 0, amount limit is disabled
  string max_amount = 2 [(gogoproto.customtype) = "cosmossdk.io/math.Int"];

  // timeframe_type defines the type of timeframe
  TimeframeType timeframe_type = 3;

  // timeframe_duration is the duration of the timeframe
  int64 timeframe_duration = 4;
}
```

## TimeframeType Enum

```protobuf
enum TimeframeType {
  TIMEFRAME_TYPE_UNSPECIFIED = 0;
  TIMEFRAME_TYPE_BLOCK = 1;  // Duration in blocks
  TIMEFRAME_TYPE_HOUR = 2;  // Duration in hours (converted to blocks)
  TIMEFRAME_TYPE_DAY = 3;   // Duration in days (converted to blocks)
}
```

## Validation

The message is validated to ensure:
1. The `authority` address matches the module's configured authority
2. All parameters are valid (checked via `params.Validate()`)
3. All required fields are provided

## Response

```protobuf
message MsgUpdateParamsResponse {}
```

An empty response indicates successful parameter update.

## Usage Example

```json
{
  "authority": "cosmos10d07y265gmmuvt4z0w9aw880jnsr700j6zn9kn",
  "params": {
    "rate_limits": [
      {
        "channel_id": "channel-0",
        "denom": "uatom",
        "supply_shift_limits": [
          {
            "max_amount": "1000000000",
            "timeframe_type": "TIMEFRAME_TYPE_DAY",
            "timeframe_duration": 1
          }
        ],
        "unique_sender_limits": [],
        "address_limits": []
      }
    ]
  }
}
```

## Governance

This message is typically submitted as a governance proposal, allowing the community to vote on rate limit changes. The authority address must sign the transaction.


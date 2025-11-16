# Custom IBC Hooks

## Overview

The Custom IBC Hooks module extends IBC transfer functionality by allowing users to execute custom actions (such as swaps and transfers) automatically when receiving IBC tokens. This enables complex cross-chain workflows in a single transaction.

## Purpose

The module enables:
- **Swap and Transfer**: Automatically swap received tokens and transfer the output to a destination
- **Swap and IBC Transfer**: Swap received tokens and send them to another chain via IBC
- **Cross-chain DeFi**: Enable seamless cross-chain DeFi operations without manual intervention

## Architecture

The module operates as an IBC middleware hook that:
1. Intercepts incoming IBC transfer packets
2. Parses hook data from the transfer memo
3. Executes the IBC transfer first (to receive the tokens)
4. Executes the custom hook actions (swap, transfer, etc.)
5. Returns an error acknowledgement if the hook fails, rolling back the entire transaction

## Key Concepts

### Hook Data Format

Hook data is specified in the IBC transfer memo using JSON format. The module supports the `swap_and_action` format, similar to Skip Protocol's implementation.

### Swap and Action

The primary hook type is `swap_and_action`, which:
1. Executes a swap operation using the received tokens
2. Performs a post-swap action (local transfer or IBC transfer)

### Intermediate Sender Address

The module derives an intermediate sender address from the IBC channel and original sender. This address is used to:
- Receive the IBC transfer tokens
- Execute the swap operation
- Send tokens to the final destination

### Atomic Execution

All operations are executed atomically:
- If the IBC transfer succeeds but the hook fails, the entire transaction is rolled back
- If the hook fails, an error acknowledgement is returned, preventing the IBC transfer from completing
- State changes are only committed if all operations succeed

## Hook Data Structure

### HookData

```go
type HookData struct {
    SwapAndAction *SwapAndAction `json:"swap_and_action,omitempty"`
}
```

### SwapAndAction

```go
type SwapAndAction struct {
    UserSwap         *UserSwap       `json:"user_swap,omitempty"`
    MinAsset         *MinAsset       `json:"min_asset,omitempty"`
    TimeoutTimestamp *uint64         `json:"timeout_timestamp,omitempty"`
    PostSwapAction   *PostSwapAction `json:"post_swap_action,omitempty"`
}
```

### UserSwap

```go
type UserSwap struct {
    SwapExactAssetIn *SwapExactAssetIn `json:"swap_exact_asset_in,omitempty"`
}
```

### SwapExactAssetIn

```go
type SwapExactAssetIn struct {
    SwapVenueName string      `json:"swap_venue_name,omitempty"`
    Operations    []Operation `json:"operations"`
}
```

### Operation

```go
type Operation struct {
    Pool     string `json:"pool"`     // Pool ID as string
    DenomIn  string `json:"denom_in"`
    DenomOut string `json:"denom_out"`
}
```

### PostSwapAction

```go
type PostSwapAction struct {
    IBCTransfer *IBCTransferInfo `json:"ibc_transfer,omitempty"`
    Transfer    *TransferInfo    `json:"transfer,omitempty"`
}
```

**Note**: Exactly one of `IBCTransfer` or `Transfer` must be specified.

## Validation Rules

1. **Post-swap action is required**: The `post_swap_action` field must be present
2. **Exactly one action**: Either `ibc_transfer` or `transfer` must be specified, but not both
3. **Swap required**: If `post_swap_action` is defined, a swap must also be defined
4. **Single-hop swaps**: Only single-operation swaps are currently supported
5. **Denom matching**: The swap's `denom_in` must match the received token denomination
6. **Channel validation**: For IBC transfers, the source channel must exist
7. **Address validation**: All addresses must be valid Bech32 addresses
8. **Capability validation**: Channel capabilities must exist for IBC transfers

## Supported Operations

### Swap Operations

- **Pool-based swaps**: Swaps through GAMM pools
- **Single-hop only**: Multi-hop swaps are not currently supported
- **Exact input**: Swaps use exact input amount with minimum output protection

### Post-Swap Actions

#### Local Transfer

Transfers the swapped tokens to a local address:

```json
{
  "transfer": {
    "to_address": "cosmos1..."
  }
}
```

#### IBC Transfer

Sends the swapped tokens to another chain via IBC:

```json
{
  "ibc_transfer": {
    "ibc_info": {
      "source_channel": "channel-0",
      "receiver": "cosmos1...",
      "memo": "...",
      "recover_address": "cosmos1..."
    }
  }
}
```

**Important**: The `recover_address` is used as an intermediate address. Tokens are first sent to this address, then IBC transferred out. This is required for proper token handling.

## Integration

The module integrates with:
- **IBC Transfer Module**: Receives and processes IBC transfer packets
- **GAMM Module**: Executes swap operations through liquidity pools
- **Bank Module**: Performs local token transfers
- **IBC Hooks Framework**: Uses the IBC hooks infrastructure for packet interception

## Error Handling

If any step fails:
1. An error acknowledgement is returned
2. The IBC transfer is rolled back
3. No state changes are committed
4. The error is logged for debugging

Common errors include:
- Invalid memo format
- Swap failure (slippage, insufficient liquidity, etc.)
- Invalid addresses
- Missing channel capabilities
- Attempting to IBC transfer BitBadges denominations (not allowed)

## Limitations

1. **Single-hop swaps only**: Multi-hop swaps are not supported
2. **GAMM pools only**: Only GAMM pool swaps are supported
3. **No BitBadges IBC transfers**: BitBadges denominations cannot be IBC transferred
4. **Required swap**: A swap is required if using post-swap actions (use packet-forward-middleware for transfers without swaps)

## Usage Example

### Swap and Local Transfer

```json
{
  "swap_and_action": {
    "user_swap": {
      "swap_exact_asset_in": {
        "swap_venue_name": "gamm",
        "operations": [
          {
            "pool": "1",
            "denom_in": "uatom",
            "denom_out": "uosmo"
          }
        ]
      }
    },
    "min_asset": {
      "native": {
        "denom": "uosmo",
        "amount": "1000000"
      }
    },
    "post_swap_action": {
      "transfer": {
        "to_address": "cosmos1abc123..."
      }
    }
  }
}
```

### Swap and IBC Transfer

```json
{
  "swap_and_action": {
    "user_swap": {
      "swap_exact_asset_in": {
        "swap_venue_name": "gamm",
        "operations": [
          {
            "pool": "1",
            "denom_in": "uatom",
            "denom_out": "uosmo"
          }
        ]
      }
    },
    "min_asset": {
      "native": {
        "denom": "uosmo",
        "amount": "1000000"
      }
    },
    "timeout_timestamp": 1234567890000000000,
    "post_swap_action": {
      "ibc_transfer": {
        "ibc_info": {
          "source_channel": "channel-0",
          "receiver": "cosmos1xyz789...",
          "recover_address": "cosmos1intermediate..."
        }
      }
    }
  }
}
```

## Security Considerations

1. **Atomic execution**: All operations are atomic - either all succeed or all fail
2. **Slippage protection**: Minimum output amounts protect against excessive slippage
3. **Address validation**: All addresses are validated before execution
4. **Channel validation**: IBC channels are verified to exist before use
5. **Capability checks**: Channel capabilities are verified before IBC transfers
6. **Error rollback**: Failed hooks roll back the entire IBC transfer


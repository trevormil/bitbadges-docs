# Introduction

## Architecture

The module operates as an IBC middleware hook that:

1. Intercepts incoming IBC transfer packets
2. Parses hook data from the transfer memo
3. Executes the IBC transfer first (to receive the tokens)
4. Executes the custom hook actions (swap, transfer, etc.)
5. Returns an error acknowledgement if the hook fails, rolling back the entire transaction

```
Warning: Native x/tokenization assets cannot be IBC transferred. Only x/bank assets can be.
```

## Key Concepts

### Hook Data Format

Hook data is specified in the IBC transfer memo using JSON format. The module supports the `swap_and_action` format, similar to Skip Protocol's implementation with a couple minor differences (some features may not be supported).

### Swap and Action

The primary hook type is `swap_and_action`, which:

1. Executes a swap operation using the received tokens
2. Performs a post-swap action (local transfer or IBC transfer)

### Intermediate Sender Address

The module derives an intermediate sender address from the IBC channel and original sender. This address is used to:

* Receive the IBC transfer tokens
* Execute the swap operation
* Send tokens to the final destination

Use here for generation if needed

<pre class="language-typescript"><code class="lang-typescript"><strong>import { deriveIntermediateSender } from 'bitbadgesjs-sdk';
</strong>
// deriveIntermediateSender('channel-0', 'osmo1...', 'bb');
export function deriveIntermediateSender(channel: string, originalSender: string, bech32Prefix: string)
</code></pre>

### Atomic Execution

All operations are executed atomically:

* If the IBC transfer succeeds but the hook fails, the entire transaction is rolled back
* If the hook fails, an error acknowledgement is returned, preventing the IBC transfer from completing
* State changes are only committed if all operations succeed

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
    UserSwap                  *UserSwap       `json:"user_swap,omitempty"`
    MinAsset                  *MinAsset       `json:"min_asset,omitempty"`
    TimeoutTimestamp          *uint64         `json:"timeout_timestamp,omitempty"`
    PostSwapAction            *PostSwapAction `json:"post_swap_action,omitempty"`
    DestinationRecoverAddress string          `json:"destination_recover_address,omitempty"`
    Affiliates                []Affiliate     `json:"affiliates,omitempty"`
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

### MinAsset

```go
type MinAsset struct {
    Native *NativeAsset `json:"native,omitempty"`
}
```

### NativeAsset

```go
type NativeAsset struct {
    Denom  string `json:"denom"`
    Amount string `json:"amount"`
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

### TransferInfo

```go
type TransferInfo struct {
    ToAddress string `json:"to_address"`
}
```

### IBCTransferInfo

```go
type IBCTransferInfo struct {
    IBCInfo *IBCInfo `json:"ibc_info,omitempty"`
}
```

### IBCInfo

```go
type IBCInfo struct {
    SourceChannel  string `json:"source_channel"`
    Receiver       string `json:"receiver"`
    Memo           string `json:"memo,omitempty"`
    RecoverAddress string `json:"recover_address,omitempty"`
}
```

### Affiliate

```go
type Affiliate struct {
    BasisPointsFee string `json:"basis_points_fee"`
    Address        string `json:"address"`
}
```

#### Affiliates

The `affiliates` field allows you to specify fee recipients who will receive a portion of the swap output as an affiliate fee. This is useful for referral programs, partnerships, or revenue sharing.

* **Optional**: If not specified, no affiliate fees are deducted
* **Basis points**: Fees are specified in basis points (1 basis point = 0.01%, 100 basis points = 1%)
* **Multiple affiliates**: You can specify multiple affiliates, each receiving their specified fee
* **Fee calculation**: Fees are calculated on the swap output amount before any post-swap actions
* **Address format**: Must be a valid Bech32 address on the destination chain

**Example**:

```json
{
    "affiliates": [
        {
            "basis_points_fee": "10", // 0.1% fee
            "address": "bb1..."
        },
        {
            "basis_points_fee": "25", // 0.25% fee
            "address": "bb1..."
        }
    ]
}
```

In this example, if the swap outputs 1,000,000 tokens:

* First affiliate receives: 1,000,000 × 0.001 = 1,000 tokens
* Second affiliate receives: 1,000,000 × 0.0025 = 2,500 tokens
* Remaining amount: 1,000,000 - 1,000 - 2,500 = 996,500 tokens

The remaining amount (996,500 tokens) is then used for the post-swap action.

#### DestinationRecoverAddress

The `destination_recover_address` field specifies what to do in the case of a swap failure. The typical behavior is that if a swap of asset A to asset B fails, the whole IBC transfer is failed (acknowledgement error) and funds (asset A) are rolled back to the source chain. However, by specifying the address here, you can let the hook know that you are okay with just keeping asset A in the specified destination address in the case of a swap failure on the destination chain. Any post-swap action will not be executed since the swap failed. It will simply be treated as a standard IBC transfer to the specified address with no swap or post-swap action.

```json
{
    "destination_recover_address": "bb1..." // Destination chain address (optional)
}
```

This only applies in the case of swap failures. Swap logic failures include, but are not limited to:

* Exceeds slippage tolerance
* Compliance not passed
* Insufficient funds (should never happen since we use the amount transferred via IBC as the input amount)
* Calculated amount out equals zero
* etc.

This is not applied in the cases below. Standard behavior is used in these cases.

* Misconfigurations of the messages (wrong prefixes, missing fields, etc.)
* Invalid pool IDs
* Standard IBC transfer failures (rate limits, timeouts, etc.)

Logic here is that in the event of a swap failure, the expected chain where the user expects funds to be recoverable is the source chain.

For example, if we have a multi-swap path like:

1. Send Asset A from BitBadges to Osmosis
2. Swap Asset A to Asset B on Osmosis
3. Send Asset B from Osmosis to BitBadges
4. Swap Asset B to Asset C on BitBadges

If Step 4 fails, the default behavior is that funds (Asset B) will be recoverable on Osmosis (bad UX). With this recovery logic, if Step 4 fails, funds (Asset B) will be recoverable on BitBadges (better UX). Minor, but it avoids confusion for the user and potentially needing to interact with other chains for recovery. It isn't a catch-all solution, but it is a good UX improvement in many cases.

## Validation Rules

1. **Post-swap action is required**: The `post_swap_action` field must be present
2. **Exactly one action**: Either `ibc_transfer` or `transfer` must be specified, but not both
3. **Swap required**: If `post_swap_action` is defined, a swap must also be defined
4. **Single-hop swaps**: Only single-operation swaps are currently supported
5. **Denom matching**: The swap's `denom_in` must match the received token denomination and the amount will automatically be the received amount
6. **Channel validation**: For IBC transfers, the source channel must exist
7. **Address validation**: All addresses must be valid Bech32 addresses
8. **Capability validation**: Channel capabilities must exist for IBC transfers

### Post-Swap Actions

#### Local Transfer

Transfers the swapped tokens to a local address:

```json
{
    "transfer": {
        "to_address": "bb1..."
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
            "receiver": "bb1...",
            "memo": "...",
            "recover_address": "bb1..."
        }
    }
}
```

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
                "swap_venue_name": "bitbadges-poolmanager",
                "operations": [
                    {
                        "pool": "1",
                        "denom_in": "ubadge",
                        "denom_out": "ibc/ABC..."
                    }
                ]
            }
        },
        "min_asset": {
            "native": {
                "denom": "ibc/ABC...",
                "amount": "1000000"
            }
        },
        "post_swap_action": {
            "transfer": {
                "to_address": "bb1..."
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
                "swap_venue_name": "bitbadges-poolmanager",
                "operations": [
                    {
                        "pool": "1",
                        "denom_in": "ubadge",
                        "denom_out": "ibc/ABC..."
                    }
                ]
            }
        },
        "min_asset": {
            "native": {
                "denom": "ibc/ABC...",
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
        },
        "destination_recover_address": "bb1...",
        "affiliates": [
            {
                "basis_points_fee": "10",
                "address": "bb1..."
            }
        ]
    }
}
```

### Swap with Affiliates

```json
{
    "swap_and_action": {
        "user_swap": {
            "swap_exact_asset_in": {
                "swap_venue_name": "bitbadges-poolmanager",
                "operations": [
                    {
                        "pool": "1",
                        "denom_in": "ubadge",
                        "denom_out": "ibc/ABC..."
                    }
                ]
            }
        },
        "min_asset": {
            "native": {
                "denom": "ibc/ABC...",
                "amount": "1000000"
            }
        },
        "post_swap_action": {
            "transfer": {
                "to_address": "bb1..."
            }
        },
        "affiliates": [
            {
                "basis_points_fee": "50", // 0.5% fee
                "address": "bb1affiliate1..."
            },
            {
                "basis_points_fee": "25", // 0.25% fee
                "address": "bb1affiliate2..."
            }
        ]
    }
}
```

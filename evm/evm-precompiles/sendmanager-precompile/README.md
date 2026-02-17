# SendManager Precompile

**Address:** `0x0000000000000000000000000000000000001003`

The SendManager Precompile enables Solidity smart contracts to send native Cosmos coins from EVM without requiring ERC20 wrapping. All accounting is kept in `x/bank` (Cosmos side).

## Quick Start

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./interfaces/ISendManagerPrecompile.sol";

contract TokenSender {
    ISendManagerPrecompile constant SENDMANAGER =
        ISendManagerPrecompile(0x0000000000000000000000000000000000001003);

    function sendTokens(
        string memory toAddress,
        string memory denom,
        uint256 amount
    ) external returns (bool) {
        string memory msgJson = string(abi.encodePacked(
            '{"to_address":"', toAddress,
            '","amount":[{"denom":"', denom,
            '","amount":"', _uintToString(amount), '"}]}'
        ));

        return SENDMANAGER.send(msgJson);
    }
}
```

## Interface

```solidity
interface ISendManagerPrecompile {
    /// @notice Send native Cosmos coins from the caller to a recipient
    /// @param msgJson JSON string matching MsgSendWithAliasRouting protobuf format
    /// @return success Whether the send succeeded
    function send(string memory msgJson) external returns (bool success);
}
```

## JSON Format

```json
{
  "to_address": "bb1xyz789...",
  "amount": [
    {"denom": "ubadge", "amount": "1000000000"},
    {"denom": "badgeslp:1:ubadge", "amount": "500000000"}
  ]
}
```

**Notes:**
- `from_address` is automatically set from `msg.sender` (cannot be spoofed)
- `to_address` must be a valid Cosmos bech32 address (`bb1...`)
- Supports both standard denoms (`ubadge`) and alias denoms (`badgeslp:...`)

## TypeScript Example

```typescript
import { ethers } from "ethers";

const SENDMANAGER_ADDRESS = "0x0000000000000000000000000000000000001003";

const sendManagerABI = [
  "function send(string memory msgJson) external returns (bool success)"
];

const sendManager = new ethers.Contract(SENDMANAGER_ADDRESS, sendManagerABI, signer);

const msgJson = JSON.stringify({
  to_address: "bb1xyz789...",
  amount: [{ denom: "ubadge", amount: "1000000000" }]
});

const tx = await sendManager.send(msgJson);
await tx.wait();
```

## Gas Costs

- **Base Gas**: 180,000 (30,000 + 150,000 buffer)
- **Per Coin**: +2,000 per coin in the `amount` array

## Error Codes

| Code | Name | Description |
|------|------|-------------|
| 1 | InvalidInput | Invalid JSON or address |
| 2 | SendFailed | Send operation failed |
| 3 | InsufficientBalance | Insufficient balance |
| 4 | InternalError | Internal error |
| 5 | Unauthorized | Unauthorized operation |

## Key Features

- Send native Cosmos coins directly from EVM without ERC20 wrapping
- All accounting kept in `x/bank` (Cosmos side)
- Supports standard denoms (`ubadge`) and alias denoms (`badgeslp:1:ubadge`)
- Recipient must be a Cosmos bech32 address (`bb1...`)
- Sender automatically set from `msg.sender`

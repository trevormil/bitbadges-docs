# IBC Backed Paths

## Overview

IBC Backed Paths enable bidirectional conversion between badge tokens and IBC coins (standard Cosmos SDK coins). This feature allows badge collections to be "backed" by IBC-denominated tokens, enabling seamless interoperability between the badge system and the broader Cosmos ecosystem.

## Core Concept

An IBC Backed Path creates a special address that acts as an intermediary for converting badge tokens to and from IBC coins. When configured, users can:

-   **Back tokens**: Send badge tokens to the special address and receive IBC coins in return
-   **Unback tokens**: Send IBC coins to the special address and receive badge tokens in return

## Key Components

### Special Address

Each IBC backed path has a **special address** that is automatically generated based on the IBC denomination. This address:

-   Is deterministically derived from the IBC denom using a hash function
-   Acts as the intermediary for all conversions
-   Is marked as a reserved protocol address
-   Holds the IBC coins that back the badge tokens

### Configuration Structure

```protobuf
message CosmosCoinBackedPath {
  string address = 1;              // Auto-generated special address (from conversion.sideA.denom)
  Conversion conversion = 2;       // Conversion structure with sideA (amount+denom) and sideB (balances)
}

message Conversion {
  ConversionSideAWithDenom sideA = 1;  // Contains amount + denom (from old ibcAmount + ibcDenom)
  repeated Balance sideB = 2;          // Badge token balances (from old balances field)
}

message ConversionSideAWithDenom {
  string amount = 1;  // IBC coin amount (from old ibcAmount)
  string denom = 2;   // IBC denomination (from old ibcDenom)
}
```

### Conversion Mechanism

The conversion uses a structured format that combines the IBC denom and amount:

```
conversion.sideA (amount, denom) = conversion.sideB[] (x/badges)
Ex: { amount: "1000000", denom: "ibc/1234..." } = [{ amount: 1n, tokenIds: [{ start: 1n, end: 1n }], ownershipTimes: UintRangeArray.FullRanges() }]
```

## Transferability Requirements

Even though this is a special address, it is still subject to the same transferability requirements as any other address. This means that you can user-gate, rate-limit, or anything else you can do with an address and its transferability.

**Important:** Collection approvals used for IBC backed path operations must have `allowedBackedMinting: true` set in their `approvalCriteria`. This flag ensures that only explicitly designated approvals can be used for backed minting operations.

This allows you to build complex minting / unwinding logic. For example:

-   $2500 per day withdrawal limit
-   Must KYC on way out
-   Or any other logic you can think of

Behind the scenes, think of this address as an external contract that updates its approvals dynamically. So, swaps are technically treated as initiated by the user but approved by the address itself.

## Configuration Requirements

### Collection Invariants

IBC backed paths are configured as **collection invariants**, meaning:

-   They can only be set during collection creation
-   They cannot be modified after creation
-   Only **one** backed path is allowed per collection

## Restrictions and Limitations

### Mint Address Restrictions

When an IBC backed path is configured:

-   Transfers **from** the Mint address are not allowed
-   All mints must be done through the IBC backed path
-   Collection approvals cannot include the Mint address in `FromListId`
-   This prevents minting tokens that would bypass the backing mechanism and cause desyncs in the system

## Technical Implementation

### Address Detection

The system automatically detects when transfers involve the special address:

-   Checks if `to` address matches the backed path address (backing)
-   Checks if `from` address matches the backed path address (unbacking)
-   Triggers the appropriate conversion logic

Note this is done from our MsgTransferTokens implementation as the entrypoint. Simply using the bank module to detect the address will not work as the bank module does not know about the IBC backed path.

### State Management

-   Badge token balances are managed normally
-   IBC coin balances are managed via the bank module
-   Special address holds IBC coins for backing operations
-   Special address technically has unlimited badge balances but only allows transfers when adequate IBC coins are present and sent or received

## Example Configuration

```json
{
    "invariants": {
        "cosmosCoinBackedPath": {
            "conversion": {
                "sideA": {
                    "amount": "1000000",
                    "denom": "ibc/1234567890ABCDEF"
                },
                "sideB": [
                    {
                        "amount": "1",
                        "tokenIds": [{ "start": "1", "end": "1" }],
                        "ownershipTimes": [
                            { "start": "1", "end": "18446744073709551615" }
                        ]
                    }
                ]
            }
        }
    }
}
```

## Security Considerations

### Reserved Protocol Address

The special address is automatically marked as a reserved protocol address:

-   Prevents accidental use in other contexts
-   Ensures proper handling by the system
-   Maintains address integrity

### Atomic Operations

All conversions are atomic:

-   Either the entire operation succeeds or fails
-   No partial conversions
-   Maintains balance consistency

## Differences from Wrapper Paths

IBC backed paths differ mainly from which IBC denom is used for minting.

| Feature | IBC Backed Path                              | Wrapper Path                             |
| ------- | -------------------------------------------- | ---------------------------------------- |
| Minting | No minting/burning (uses existing IBC denom) | Minting/burning of generated denom (new) |

## Summary

IBC Backed Paths provide a powerful mechanism for connecting badge tokens to the broader Cosmos ecosystem through IBC coins. By enabling bidirectional conversion between badges and IBC-denominated assets, they unlock cross-chain interoperability, DeFi integration, and liquidity provision while maintaining the unique features and capabilities of the badge system.

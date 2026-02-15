# Gamm Precompile - Common Gotchas

This document covers common issues and gotchas when working with the Gamm Precompile.

## Table of Contents

1. [Pool ID Format](#pool-id-format)
2. [Cosmos Dec Handling](#cosmos-dec-handling)
3. [Amount String Formatting](#amount-string-formatting)
4. [Return Value Decoding](#return-value-decoding)
5. [Slippage Protection](#slippage-protection)
6. [Multi-Hop Swaps](#multi-hop-swaps)

## Pool ID Format

### Issue

Pool IDs are `uint64` in Go/Cosmos SDK, but must be **strings in JSON**.

### Correct Usage

```solidity
// ✅ Correct - Pool ID as string in JSON
string memory json = string(abi.encodePacked(
    '{"pool_id":"1",',  // Note: "1" not 1
    '"share_out_amount":"1000000",',
    // ...
));

// ❌ Wrong - Pool ID as number
string memory json = string(abi.encodePacked(
    '{"pool_id":1,',  // This will fail!
    // ...
));
```

### Helper Function

```solidity
// Use helper to ensure correct format
string memory json = GammJSONHelpers.joinPoolJSON(
    1,  // uint64 poolId - helper converts to "1" in JSON
    shareOutAmount,
    // ...
);
```

### Why This Matters

- Protobuf JSON encoding requires numbers as strings for large values
- Prevents precision loss for large pool IDs
- Consistent with Cosmos SDK JSON format

## Cosmos Dec Handling

### Issue

Cosmos SDK uses `sdk.Dec` (decimal) for calculations, but amounts in JSON are strings representing integers in the smallest unit.

### Understanding Cosmos Dec

Cosmos Dec is a fixed-point decimal type with 18 decimal places. However, in JSON:
- **Amounts are always strings** representing the smallest unit
- Example: `"1000000000"` for 1 token with 9 decimals
- No decimal point in JSON - all amounts are integers as strings

### Correct Format

```solidity
// ✅ Correct - Amount as string
string memory json = string(abi.encodePacked(
    '{"token_in":{',
    '"denom":"ubadge",',
    '"amount":"1000000000"',  // String, no decimal point
    '}}'
));

// ❌ Wrong - Amount as number or with decimal
string memory json = string(abi.encodePacked(
    '{"token_in":{',
    '"denom":"ubadge",',
    '"amount":1000000000',  // Number - will fail!
    // or
    '"amount":"1.0"',  // Decimal point - will fail!
    '}}'
));
```

### Conversion Helpers

```solidity
// Convert human-readable amount to smallest unit string
function amountToJson(uint256 amount, uint8 decimals) internal pure returns (string memory) {
    // amount is already in smallest unit (e.g., 1000000000 for 1 token with 9 decimals)
    return GammJSONHelpers.uintToString(amount);
}

// Example: 1 BADGE with 9 decimals = 1000000000
string memory amountJson = amountToJson(1e9, 9);  // Returns "1000000000"
```

### Common Mistakes

1. **Using decimal points in JSON**
   ```solidity
   // ❌ Wrong
   '"amount":"1.5"'
   
   // ✅ Correct
   '"amount":"1500000000"'  // 1.5 tokens with 9 decimals
   ```

2. **Using number type instead of string**
   ```solidity
   // ❌ Wrong
   '"amount":1000000000'
   
   // ✅ Correct
   '"amount":"1000000000"'
   ```

3. **Not accounting for token decimals**
   ```solidity
   // ❌ Wrong - assumes 1 token = 1 unit
   uint256 amount = 1;  // This is 0.000000001 tokens with 9 decimals!
   
   // ✅ Correct - account for decimals
   uint256 amount = 1 * 10**9;  // 1 token with 9 decimals
   ```

## Amount String Formatting

### All Amounts Must Be Strings

Every amount field in JSON must be a string, not a number:

```solidity
// ✅ Correct
{
  "pool_id": "1",
  "share_out_amount": "1000000",  // String
  "token_in_maxs": [
    {
      "denom": "ubadge",
      "amount": "1000000000"  // String
    }
  ]
}

// ❌ Wrong
{
  "pool_id": "1",
  "share_out_amount": 1000000,  // Number - will fail!
  "token_in_maxs": [
    {
      "denom": "ubadge",
      "amount": 1000000000  // Number - will fail!
    }
  ]
}
```

### Helper Functions

Always use helper functions to ensure correct formatting:

```solidity
// Helper converts uint256 to string automatically
string memory json = GammJSONHelpers.joinPoolJSON(
    poolId,
    shareOutAmount,  // uint256 - helper converts to string
    tokenDenom,
    tokenAmount      // uint256 - helper converts to string
);
```

## Return Value Decoding

### Issue

Return values from precompile methods may be in different formats depending on the method.

### Tuple Returns

Methods like `joinPool` return tuples:

```solidity
// Method signature
function joinPool(string calldata msgJson) 
    external 
    returns (uint256 shareOutAmount, GammTypes.Coin[] memory tokenIn);

// Usage
(uint256 shares, GammTypes.Coin[] memory tokens) = GAMM.joinPool(json);
```

### Coin Array Structure

Coin arrays are returned as structs:

```solidity
struct Coin {
    string denom;
    uint256 amount;
}

// Access values
for (uint i = 0; i < tokens.length; i++) {
    string memory denom = tokens[i].denom;
    uint256 amount = tokens[i].amount;
}
```

### Protobuf Bytes

Some query methods return protobuf-encoded bytes:

```solidity
bytes memory poolBytes = GAMM.getPool(json);
// Decode off-chain using TypeScript SDK or protobuf library
```

## Slippage Protection

### Always Use Minimum/Maximum Amounts

Protect against slippage by specifying limits:

### Join Pool - Use `token_in_maxs`

```solidity
// ✅ Correct - Specify maximum amounts you're willing to pay
string memory json = GammJSONHelpers.joinPoolJSON(
    poolId,
    desiredShares,
    denom1,
    maxAmount1,  // Maximum you're willing to pay
    denom2,
    maxAmount2   // Maximum you're willing to pay
);

// ❌ Wrong - No slippage protection
// If pool price changes, you might pay more than expected
```

### Exit Pool - Use `token_out_mins`

```solidity
// ✅ Correct - Specify minimum amounts you want to receive
string memory json = GammJSONHelpers.exitPoolJSON(
    poolId,
    shareAmount,
    minTokenOut1,  // Minimum you want to receive
    minTokenOut2   // Minimum you want to receive
);

// ❌ Wrong - No slippage protection
// If pool price changes, you might receive less than expected
```

### Swap - Use `token_out_min_amount`

```solidity
// ✅ Correct - Specify minimum output amount
string memory json = GammJSONHelpers.swapExactAmountInJSON(
    poolId,
    tokenInDenom,
    tokenInAmount,
    tokenOutDenom,
    minTokenOutAmount  // Minimum you want to receive
);

// ❌ Wrong - No slippage protection
// If pool price changes, you might receive less than expected
```

### Calculating Slippage Tolerance

```solidity
// Example: 1% slippage tolerance
uint256 tokenInAmount = 1000000000;  // 1 token
uint256 minTokenOutAmount = tokenInAmount * 99 / 100;  // 0.99 tokens (1% slippage)

string memory json = GammJSONHelpers.swapExactAmountInJSON(
    poolId,
    tokenInDenom,
    tokenInAmount,
    tokenOutDenom,
    minTokenOutAmount
);
```

## Multi-Hop Swaps

### Issue

When using multiple routes, account for cumulative slippage across all hops.

### Correct Usage

```solidity
// Multi-hop: A → B → C
// Slippage accumulates: 1% per hop = ~2% total for 2 hops

uint256 tokenInAmount = 1000000000;
uint256 minTokenOutAmount = tokenInAmount * 98 / 100;  // 2% slippage tolerance

string memory json = GammJSONHelpers.multiHopSwapJSON(
    poolIds,      // [pool1, pool2]
    tokenDenoms,  // ["A", "B", "C"]
    tokenInAmount,
    minTokenOutAmount
);
```

### Route Format

```solidity
// Routes array format
{
  "routes": [
    {
      "pool_id": "1",
      "token_out_denom": "uatom"
    },
    {
      "pool_id": "2",
      "token_out_denom": "ustake"
    }
  ],
  "token_in": {
    "denom": "ubadge",
    "amount": "1000000000"
  },
  "token_out_min_amount": "1800000000",  // Account for 2-hop slippage
  "affiliates": []
}
```

## Common Error Patterns

### Error: "poolId cannot be zero"

**Cause:** Pool ID is 0 or not provided correctly.

**Fix:**
```solidity
// ✅ Correct
require(poolId > 0, "Invalid pool ID");
string memory json = GammJSONHelpers.joinPoolJSON(poolId, ...);

// ❌ Wrong
uint64 poolId = 0;  // Will fail validation
```

### Error: "amount must be greater than zero"

**Cause:** Amount is zero, negative, or not formatted correctly.

**Fix:**
```solidity
// ✅ Correct
require(amount > 0, "Amount must be positive");
string memory amountStr = GammJSONHelpers.uintToString(amount);

// ❌ Wrong
uint256 amount = 0;  // Will fail validation
```

### Error: "invalid JSON syntax"

**Cause:** JSON is malformed or uses wrong types.

**Fix:**
```solidity
// ✅ Correct - Use helper functions
string memory json = GammJSONHelpers.joinPoolJSON(...);

// ❌ Wrong - Manual construction prone to errors
string memory json = string(abi.encodePacked('{"pool_id":', ...));  // Missing quotes, wrong types
```

### Error: "denom cannot be empty"

**Cause:** Denomination string is empty.

**Fix:**
```solidity
// ✅ Correct
require(bytes(denom).length > 0, "Denom cannot be empty");
string memory json = GammJSONHelpers.joinPoolJSON(poolId, shareAmount, denom, amount);

// ❌ Wrong
string memory denom = "";  // Will fail validation
```

## Best Practices Summary

1. **Always use helper functions** - They handle type conversions correctly
2. **Validate inputs** - Check pool IDs, amounts, and denominations before building JSON
3. **Use slippage protection** - Always specify minimum/maximum amounts
4. **Account for decimals** - Convert human-readable amounts to smallest unit
5. **Handle return values correctly** - Understand tuple vs single return types
6. **Test with small amounts first** - Verify behavior before large transactions

## Debugging Tips

### 1. Log JSON Before Sending

```solidity
string memory json = GammJSONHelpers.joinPoolJSON(...);
console.log("JSON:", json);  // Verify format
```

### 2. Check Return Values

```solidity
(uint256 shares, GammTypes.Coin[] memory tokens) = GAMM.joinPool(json);
console.log("Shares:", shares);
for (uint i = 0; i < tokens.length; i++) {
    console.log("Token:", tokens[i].denom, tokens[i].amount);
}
```

### 3. Use Static Calls for Testing

```solidity
// Test without sending transaction
(uint256 shares, ) = GAMM.joinPool.staticCall(json);
console.log("Expected shares:", shares);
```

## See Also

- [Gamm Precompile README](README.md) - Overview and examples
- [Complete API Reference](../../../contracts/docs/GAMM_PRECOMPILE.md) - Full method documentation
- [Developer Guide](../developer-guide.md) - General EVM integration guide











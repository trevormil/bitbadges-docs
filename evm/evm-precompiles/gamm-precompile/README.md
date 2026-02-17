# Gamm Precompile

**Address:** `0x0000000000000000000000000000000000001002`

The Gamm Precompile enables Solidity smart contracts to interact with liquidity pools through a standardized EVM interface. It provides both transaction methods (state-changing operations) and query methods (read-only operations) for managing liquidity pools, swaps, and pool information.

## Quick Start

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./interfaces/IGammPrecompile.sol";
import "./libraries/GammJSONHelpers.sol";

contract MyPoolContract {
    IGammPrecompile constant GAMM = 
        IGammPrecompile(0x0000000000000000000000000000000000001002);
    
    // Join a pool
    function joinPool(
        uint64 poolId,
        uint256 shareOutAmount,
        string memory tokenDenom,
        uint256 tokenAmount
    ) external returns (uint256 sharesReceived) {
        string memory msgJson = GammJSONHelpers.joinPoolJSON(
            poolId,
            shareOutAmount,
            tokenDenom,
            tokenAmount
        );
        
        (sharesReceived, ) = GAMM.joinPool(msgJson);
        return sharesReceived;
    }
    
    // Swap tokens
    function swap(
        uint64 poolId,
        string memory tokenInDenom,
        uint256 tokenInAmount,
        string memory tokenOutDenom,
        uint256 minTokenOutAmount
    ) external returns (uint256 tokenOutAmount) {
        string memory msgJson = GammJSONHelpers.swapExactAmountInJSON(
            poolId,
            tokenInDenom,
            tokenInAmount,
            tokenOutDenom,
            minTokenOutAmount
        );
        
        return GAMM.swapExactAmountIn(msgJson);
    }
}
```

## Why JSON?

The precompile uses JSON strings instead of Solidity structs because:

1. **Protobuf Compatibility**: Direct mapping to Cosmos SDK protobuf messages
2. **Flexibility**: Easy to extend without breaking changes
3. **Type Safety**: JSON helpers provide compile-time safety
4. **Simplicity**: Single parameter per method, easy to understand

## Core Concepts

### 1. All Methods Accept JSON Strings

Every precompile method accepts a single `string calldata msgJson` parameter that matches the protobuf JSON format:

```solidity
// ✅ Correct - JSON string
string memory json = GammJSONHelpers.joinPoolJSON(...);
(uint256 shares, ) = GAMM.joinPool(json);

// ❌ Wrong - struct parameters (old interface)
GAMM.joinPool(poolId, shareOutAmount, tokenInMaxs);
```

### 2. Use Helper Libraries

The `GammJSONHelpers` library provides type-safe functions to construct JSON strings:

```solidity
import "./libraries/GammJSONHelpers.sol";

// Simple operations
string memory json = GammJSONHelpers.getPoolJSON(poolId);
bytes memory pool = GAMM.getPool(json);

// Complex operations
string memory swapJson = GammJSONHelpers.swapExactAmountInJSON(...);
uint256 amountOut = GAMM.swapExactAmountIn(swapJson);
```

## Common Patterns

### Pattern 1: Join Pool

```solidity
function joinPool(
    uint64 poolId,
    uint256 desiredShares,
    string memory denom1,
    uint256 amount1,
    string memory denom2,
    uint256 amount2
) external returns (uint256 sharesReceived) {
    string memory msgJson = GammJSONHelpers.joinPoolJSON(
        poolId,
        desiredShares,
        denom1,
        amount1,
        denom2,
        amount2
    );
    
    (sharesReceived, ) = GAMM.joinPool(msgJson);
    return sharesReceived;
}
```

### Pattern 2: Exit Pool

```solidity
function exitPool(
    uint64 poolId,
    uint256 shareAmount
) external returns (uint256[] memory amountsOut) {
    string memory msgJson = GammJSONHelpers.exitPoolJSON(
        poolId,
        shareAmount
    );
    
    GammTypes.Coin[] memory tokensOut = GAMM.exitPool(msgJson);
    
    // Convert to array
    amountsOut = new uint256[](tokensOut.length);
    for (uint i = 0; i < tokensOut.length; i++) {
        amountsOut[i] = tokensOut[i].amount;
    }
    
    return amountsOut;
}
```

### Pattern 3: Single-Hop Swap

```solidity
function swapTokens(
    uint64 poolId,
    string memory tokenInDenom,
    uint256 tokenInAmount,
    string memory tokenOutDenom,
    uint256 minTokenOutAmount
) external returns (uint256 tokenOutAmount) {
    string memory msgJson = GammJSONHelpers.swapExactAmountInJSON(
        poolId,
        tokenInDenom,
        tokenInAmount,
        tokenOutDenom,
        minTokenOutAmount
    );
    
    return GAMM.swapExactAmountIn(msgJson);
}
```

### Pattern 4: Multi-Hop Swap

```solidity
function multiHopSwap(
    uint64[] memory poolIds,
    string[] memory tokenDenoms,
    uint256 tokenInAmount,
    uint256 minTokenOutAmount
) external returns (uint256 finalAmountOut) {
    string memory msgJson = GammJSONHelpers.multiHopSwapJSON(
        poolIds,
        tokenDenoms,
        tokenInAmount,
        minTokenOutAmount
    );
    
    return GAMM.swapExactAmountIn(msgJson);
}
```

## Available Methods

### Transaction Methods

- `joinPool(string calldata msgJson) → (uint256 shareOutAmount, Coin[] memory tokenIn)`
- `exitPool(string calldata msgJson) → Coin[] memory tokenOut`
- `swapExactAmountIn(string calldata msgJson) → uint256 tokenOutAmount`
- `swapExactAmountInWithIBCTransfer(string calldata msgJson) → uint256 tokenOutAmount`

### Query Methods

- `getPool(string calldata msgJson) → bytes`
- `getPools(string calldata msgJson) → bytes`
- `getPoolType(string calldata msgJson) → string`
- `calcJoinPoolNoSwapShares(string calldata msgJson) → (Coin[] memory tokensOut, uint256 sharesOut)`
- `calcExitPoolCoinsFromShares(string calldata msgJson) → Coin[] memory tokensOut`
- `calcJoinPoolShares(string calldata msgJson) → (uint256 shareOutAmount, Coin[] memory tokensOut)`
- `getPoolParams(string calldata msgJson) → bytes`
- `getTotalShares(string calldata msgJson) → Coin memory totalShares`
- `getTotalLiquidity(string calldata msgJson) → Coin[] memory liquidity`

See [Common Gotchas](gotchas.md) for important details about pool IDs, Cosmos Dec handling, and other common issues.

## Helper Library Functions

The `GammJSONHelpers` library provides functions for all common operations:

### Pool Operations
- `joinPoolJSON(...)` - Build join pool JSON
- `exitPoolJSON(...)` - Build exit pool JSON
- `getPoolJSON(poolId)` - Build pool query JSON

### Swap Operations
- `swapExactAmountInJSON(...)` - Build swap JSON
- `multiHopSwapJSON(...)` - Build multi-hop swap JSON

### Query Operations
- `getPoolJSON(poolId)` - Build pool query JSON
- `calcJoinPoolSharesJSON(...)` - Build calculation query JSON

## Return Value Handling

### Direct Returns (uint256)

Some methods return `uint256` directly:

```solidity
uint256 tokenOutAmount = GAMM.swapExactAmountIn(swapJson);
```

### Tuple Returns

Some methods return tuples:

```solidity
(uint256 shares, GammTypes.Coin[] memory tokens) = GAMM.joinPool(joinJson);
```

### Protobuf Bytes

Some query methods return protobuf-encoded `bytes`. Decode off-chain using the TypeScript SDK or emit events for indexing.

## Security Considerations

1. **Automatic Sender Field**: The `sender` field is automatically set from `msg.sender` and cannot be spoofed
2. **JSON Validation**: Invalid JSON will revert with clear error messages
3. **Slippage Protection**: Always use minimum/maximum amounts to protect against slippage
4. **Type Safety**: Use helper functions to ensure correct JSON structure

## Best Practices

### 1. Always Use Helper Functions

```solidity
// ✅ Good - Type-safe and readable
string memory json = GammJSONHelpers.joinPoolJSON(...);

// ❌ Bad - Error-prone manual construction
string memory json = string(abi.encodePacked('{"pool_id":"', ...));
```

### 2. Validate Inputs Before Building JSON

```solidity
function joinPool(uint64 poolId, uint256 shares) external {
    require(poolId > 0, "Invalid pool ID");
    require(shares > 0, "Invalid share amount");
    
    // Now build JSON
    string memory json = GammJSONHelpers.joinPoolJSON(...);
    GAMM.joinPool(json);
}
```

### 3. Handle Slippage

```solidity
// Always specify minimum/maximum amounts
string memory exitJson = GammJSONHelpers.exitPoolJSON(
    poolId,
    shareAmount,
    minTokenOutAmounts  // Protect against slippage
);
```

## Common Gotchas

See [Common Gotchas](gotchas.md) for detailed information about:
- Pool ID format (uint64 vs string)
- Cosmos Dec handling
- Amount string formatting
- Return value decoding

## Next Steps

- Read [Common Gotchas](gotchas.md) for important details
- Explore [Example Contracts](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts/examples)
- Review the [Complete API Reference](../../../contracts/docs/GAMM_PRECOMPILE.md)

## Resources

- [Setup & Configuration](../setup-and-configuration.md)
- [Developer Guide](../developer-guide.md)
- [Architecture](../architecture.md)
- [Gamm Module Documentation](../../../x-gamm/README.md)

























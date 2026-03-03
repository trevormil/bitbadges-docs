# Solidity Developer Quickstart

This guide gets you from zero to building on BitBadges precompiles in 5 minutes.

## TL;DR - The Essentials

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./interfaces/ITokenizationPrecompile.sol";
import "./libraries/TokenizationJSONHelpers.sol";

contract MyToken {
    // Precompile address (same on all BitBadges networks)
    ITokenizationPrecompile constant PRECOMPILE =
        ITokenizationPrecompile(0x0000000000000000000000000000000000001001);

    function transfer(uint256 collectionId, address to, uint256 amount) external {
        address[] memory recipients = new address[](1);
        recipients[0] = to;

        // CRITICAL: Use FOREVER, not type(uint256).max!
        string memory tokenIds = TokenizationJSONHelpers.uintRangeToJson(1, 1);
        string memory times = TokenizationJSONHelpers.uintRangeToJson(
            1,
            TokenizationJSONHelpers.FOREVER  // NOT type(uint256).max!
        );

        string memory json = TokenizationJSONHelpers.transferTokensJSON(
            collectionId, recipients, amount, tokenIds, times
        );

        require(PRECOMPILE.transferTokens(json), "Transfer failed");
    }
}
```

---

## 1. Critical Constants

**BitBadges uses uint64 internally.** Using `type(uint256).max` will cause errors.

```solidity
// ❌ WRONG - Will fail with "range overflow" error
TokenizationJSONHelpers.uintRangeToJson(1, type(uint256).max);

// ✅ CORRECT - Use the FOREVER constant
TokenizationJSONHelpers.uintRangeToJson(1, TokenizationJSONHelpers.FOREVER);
```

**Available Constants:**

| Constant | Value | When to Use |
|----------|-------|-------------|
| `FOREVER` | `18446744073709551615` | Ownership that never expires |
| `MAX_TIME` | `18446744073709551615` | Maximum timestamp |
| `MAX_ID` | `18446744073709551615` | Maximum token ID |
| `MIN_ID` | `1` | Minimum ID (ranges start at 1, not 0) |

---

## 2. Precompile Addresses

| Precompile | Address | Purpose |
|------------|---------|---------|
| Tokenization | `0x0000000000000000000000000000000000001001` | Collections, transfers, balances |
| GAMM | `0x0000000000000000000000000000000000001002` | Liquidity pools, swaps |
| SendManager | `0x0000000000000000000000000000000000001003` | Escrow, scheduled sends |

---

## 3. Why JSON?

BitBadges precompiles use JSON strings instead of Solidity structs:

```solidity
// This is how BitBadges precompiles work:
precompile.transferTokens('{"collectionId":"1",...}');

// NOT like traditional ERC20:
token.transfer(to, amount);  // BitBadges doesn't use this pattern
```

**Why?**
- Direct compatibility with Cosmos SDK protobuf messages
- Flexible and extensible without breaking changes
- Same format works across EVM and Cosmos interfaces

**Don't worry:** Helper libraries construct the JSON for you - you rarely write JSON manually.

---

## 4. Import the Libraries

```solidity
// Interface for calling the precompile
import "./interfaces/ITokenizationPrecompile.sol";

// Helpers for constructing JSON (ALWAYS use these)
import "./libraries/TokenizationJSONHelpers.sol";

// Helpers for constructing structs
import "./libraries/TokenizationHelpers.sol";

// Type definitions
import "./types/TokenizationTypes.sol";

// Error handling
import "./libraries/TokenizationErrors.sol";
```

---

## 5. Common Patterns

### Pattern 1: Transfer Tokens

```solidity
function transfer(
    uint256 collectionId,
    address to,
    uint256 amount,
    uint256 tokenId
) external returns (bool) {
    address[] memory recipients = new address[](1);
    recipients[0] = to;

    string memory tokenIds = TokenizationJSONHelpers.uintRangeToJson(tokenId, tokenId);
    string memory times = TokenizationJSONHelpers.uintRangeToJson(
        1, TokenizationJSONHelpers.FOREVER
    );

    string memory json = TokenizationJSONHelpers.transferTokensJSON(
        collectionId, recipients, amount, tokenIds, times
    );

    return PRECOMPILE.transferTokens(json);
}
```

### Pattern 2: Check Balance

```solidity
function balanceOf(uint256 collectionId, address user) external view returns (uint256) {
    string memory json = TokenizationJSONHelpers.getBalanceAmountJSON(
        collectionId,
        user,
        1,  // tokenId
        block.timestamp  // ownershipTime
    );

    return PRECOMPILE.getBalanceAmount(json);
}
```

### Pattern 3: Create Collection

```solidity
function createCollection() external returns (uint256) {
    string memory validTokenIds = TokenizationJSONHelpers.uintRangeToJson(
        1, 1000  // Token IDs 1-1000
    );

    string memory metadata = TokenizationJSONHelpers.collectionMetadataToJson(
        "ipfs://my-metadata", ""
    );

    string memory balances = TokenizationJSONHelpers.simpleUserBalanceStoreToJson(
        true, true, false  // auto-approve settings
    );

    string[] memory standards = new string[](0);
    string memory standardsJson = TokenizationJSONHelpers.stringArrayToJson(standards);

    string memory json = TokenizationJSONHelpers.createCollectionJSON(
        validTokenIds,
        TokenizationJSONHelpers.addressToString(address(this)),  // manager
        metadata,
        balances,
        "{}",  // permissions (empty = default)
        standardsJson,
        "",    // customData
        false  // isArchived
    );

    return PRECOMPILE.createCollection(json);
}
```

### Pattern 4: KYC Registry (Dynamic Store)

```solidity
uint256 public kycStoreId;

function initKYC() external {
    string memory json = TokenizationJSONHelpers.createDynamicStoreJSON(
        false,  // default: not KYC'd
        "", ""  // metadata
    );
    kycStoreId = PRECOMPILE.createDynamicStore(json);
}

function setKYC(address user, bool status) external {
    string memory json = TokenizationJSONHelpers.setDynamicStoreValueJSON(
        kycStoreId, user, status
    );
    PRECOMPILE.setDynamicStoreValue(json);
}
```

---

## 6. Error Handling

```solidity
import "./libraries/TokenizationErrors.sol";

contract MyContract {
    function safeTransfer(...) external {
        // Validate before calling precompile
        TokenizationErrors.requireValidCollectionId(collectionId);

        try PRECOMPILE.transferTokens(json) returns (bool success) {
            require(success, "Transfer failed");
        } catch Error(string memory reason) {
            revert(reason);
        }
    }
}
```

**Common Errors:**
- `"range overflow"` → Use `FOREVER` instead of `type(uint256).max`
- `"address cannot be empty"` → Check JSON field names
- `"failed to unmarshal"` → Invalid JSON format

See [Error Handling](tokenization-precompile/errors.md) for full error reference.

---

## 7. UintRange Explained

Token IDs and ownership times use ranges:

```solidity
// Single value: token ID 5
TokenizationJSONHelpers.uintRangeToJson(5, 5);
// Produces: [{"start":"5","end":"5"}]

// Range: token IDs 1-100
TokenizationJSONHelpers.uintRangeToJson(1, 100);
// Produces: [{"start":"1","end":"100"}]

// Forever ownership (never expires)
TokenizationJSONHelpers.uintRangeToJson(1, TokenizationJSONHelpers.FOREVER);
// Produces: [{"start":"1","end":"18446744073709551615"}]

// Multiple ranges
uint256[] memory starts = new uint256[](2);
uint256[] memory ends = new uint256[](2);
starts[0] = 1; ends[0] = 100;
starts[1] = 200; ends[1] = 300;
TokenizationJSONHelpers.uintRangeArrayToJson(starts, ends);
// Produces: [{"start":"1","end":"100"},{"start":"200","end":"300"}]
```

---

## 8. Address Formats

BitBadges supports both EVM and Cosmos addresses:

```solidity
// In Solidity, use EVM addresses (0x...)
address user = 0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb;

// Convert to Cosmos bech32 format if needed
string memory bech32 = PRECOMPILE.convertEvmAddressToBech32(user);
// Returns: "bb1ws5a0nfzue5cxetfmx56y0gu30aw7h08agd7fg"

// Convert back
address evm = PRECOMPILE.convertBech32ToEvmAddress("bb1...");
```

---

## 9. Utility Methods

The precompile includes helpful utilities:

```solidity
// Address conversion
string memory bech32 = PRECOMPILE.convertEvmAddressToBech32(evmAddress);
address evm = PRECOMPILE.convertBech32ToEvmAddress(bech32Address);

// Range checks
bool inRange = PRECOMPILE.rangeContains(10, 20, 15);  // true
bool overlap = PRECOMPILE.rangesOverlap(10, 20, 15, 25);  // true

// Search in range array
bool found = PRECOMPILE.searchInRanges('[{"start":"1","end":"100"}]', 50);

// Get balance for specific token/time
uint256 amount = PRECOMPILE.getBalanceForIdAndTime(balancesJson, tokenId, timestamp);

// Get reserved list ID for address
string memory listId = PRECOMPILE.getReservedListId(userAddress);
```

---

## 10. Quick Reference

### Helper Libraries
| Library | Purpose |
|---------|---------|
| `TokenizationJSONHelpers` | Build JSON strings for precompile calls |
| `TokenizationHelpers` | Build structs, constants, utilities |
| `TokenizationErrors` | Input validation, custom errors |
| `TokenizationTypes` | Type definitions |

### Key Methods
| Method | Returns | Purpose |
|--------|---------|---------|
| `transferTokens(json)` | `bool` | Transfer tokens |
| `createCollection(json)` | `uint256` | Create collection, returns ID |
| `getBalanceAmount(json)` | `uint256` | Get balance amount |
| `createDynamicStore(json)` | `uint256` | Create store, returns ID |

### Constants
| Constant | Value | Location |
|----------|-------|----------|
| `FOREVER` | `type(uint64).max` | JSONHelpers, Helpers |
| `MAX_TIME` | `type(uint64).max` | JSONHelpers, Helpers |
| `MAX_ID` | `type(uint64).max` | JSONHelpers, Helpers |
| `MIN_ID` | `1` | JSONHelpers, Helpers |

---

## Next Steps

1. **[Full API Reference](tokenization-precompile/API.md)** - All methods documented
2. **[Error Handling](tokenization-precompile/errors.md)** - Debug common issues
3. **[Example Contracts](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts/examples)** - Real implementations
4. **[GAMM Precompile](gamm-precompile/README.md)** - Liquidity pools and swaps

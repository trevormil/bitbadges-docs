# Error Handling & Debugging

This guide covers common errors, debugging techniques, and solutions for the Tokenization Precompile.

## Critical: uint64 vs uint256

**The #1 cause of errors for new developers.**

BitBadges uses `uint64` internally for timestamps and IDs. Using `uint256` max values will cause errors.

```solidity
// ❌ WRONG - Will cause "range overflow" error
string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(1, type(uint256).max);

// ✅ CORRECT - Use the FOREVER constant
string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(
    1,
    TokenizationJSONHelpers.FOREVER  // = type(uint64).max = 18446744073709551615
);
```

**Available Constants (in TokenizationJSONHelpers and TokenizationHelpers):**

| Constant | Value | Use Case |
|----------|-------|----------|
| `FOREVER` | `18446744073709551615` | Ownership times that never expire |
| `MAX_TIME` | `18446744073709551615` | Maximum valid timestamp |
| `MAX_ID` | `18446744073709551615` | Maximum valid token ID |
| `MIN_ID` | `1` | Minimum valid ID (ranges typically start at 1) |
| `FOREVER_STR` | `"18446744073709551615"` | For direct JSON string use |

---

## Error Handling in Solidity

### Basic Error Handling

```solidity
function safeTransfer(...) external {
    try precompile.transferTokens(json) returns (bool success) {
        require(success, "Transfer failed");
    } catch Error(string memory reason) {
        // Precompile error with message
        revert(reason);
    } catch (bytes memory lowLevelData) {
        // Low-level error - decode if possible
        revert("Unknown precompile error");
    }
}
```

### Using TokenizationErrors Library

```solidity
import "./libraries/TokenizationErrors.sol";

contract MyContract {
    using TokenizationErrors for *;

    function transfer(...) external {
        // Validate inputs before calling precompile
        TokenizationErrors.requireValidCollectionId(collectionId);
        TokenizationErrors.requireNonEmptyString(json, "JSON");

        bool success = precompile.transferTokens(json);
        require(success, "Transfer failed");
    }
}
```

---

## Common Errors and Solutions

### 1. Range Value Overflow

**Error:**
```
precompile error [code=4]: transaction failed: invalid balance times:
range at index 0 has end 115792089237316195423570985008687907853269984665640564039457584007913129639935
greater than max 18446744073709551615
```

**Cause:** Using `type(uint256).max` instead of `type(uint64).max`.

**Solution:**
```solidity
// Use the FOREVER constant
string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(
    1,
    TokenizationJSONHelpers.FOREVER
);

// Or use the MAX_TIME/MAX_ID constants from TokenizationHelpers
uint64 maxTime = TokenizationHelpers.MAX_TIME;
```

---

### 2. Address Cannot Be Empty

**Error:**
```
precompile error [code=1]: invalid input parameters: address cannot be empty
```

**Cause:** Wrong field name in JSON, or empty address.

**Solution:**
```solidity
// ❌ Wrong - "userAddress" is not recognized
'{"storeId":"1","userAddress":"0x...","value":true}'

// ✅ Correct - use "address"
'{"storeId":"1","address":"0x...","value":true}'

// ✅ Or use the helper
string memory json = TokenizationJSONHelpers.setDynamicStoreValueJSON(
    storeId,
    userAddress,  // address type, not string
    true
);
```

---

### 3. Failed to Unmarshal JSON

**Error:**
```
failed to unmarshal JSON for method X: precompile error [code=...]
```

**Causes:**
- Invalid JSON syntax (missing quotes, commas, brackets)
- Wrong field types (numbers as strings vs raw, booleans as strings)
- Missing required fields

**Solution - Check JSON Format:**

| Type | Correct | Wrong |
|------|---------|-------|
| Numbers | `"123"` | `123` |
| Booleans | `true` / `false` | `"true"` / `"false"` |
| Addresses | `"0x742d..."` | `0x742d...` |
| Ranges | `[{"start":"1","end":"100"}]` | `[{start:1,end:100}]` |

**Correct Example:**
```json
{
  "storeId": "123",
  "address": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "value": true
}
```

---

### 4. Collection Not Found

**Error:**
```
precompile error [code=5]: collection not found: collection 12345 does not exist
```

**Cause:** The collection ID doesn't exist.

**Solution:**
```solidity
// Check collection exists before operations
try precompile.getCollection(TokenizationJSONHelpers.getCollectionJSON(collectionId)) {
    // Collection exists, proceed
} catch {
    revert("Collection does not exist");
}
```

---

### 5. Insufficient Balance

**Error:**
```
precompile error [code=6]: insufficient balance: user has 50 but tried to transfer 100
```

**Cause:** Attempting to transfer more tokens than the user owns.

**Solution:**
```solidity
// Check balance before transfer
uint256 balance = precompile.getBalanceAmount(
    TokenizationJSONHelpers.getBalanceAmountJSON(
        collectionId,
        msg.sender,
        tokenId,
        block.timestamp
    )
);
require(balance >= amount, "Insufficient balance");
```

---

### 6. Not Authorized / Permission Denied

**Error:**
```
precompile error [code=7]: not authorized: caller is not the collection manager
```

**Causes:**
- Caller is not the collection manager
- Missing approval for transfer
- Permission locked in collection permissions
- Time window expired for operation

**Debugging Steps:**
1. Check who the collection manager is
2. Verify approvals are set correctly
3. Check if permissions are locked (permanentlyForbiddenTimes)
4. Verify current time is within allowed window

**Solution:**
```solidity
// Get collection to check manager
bytes memory collectionBytes = precompile.getCollection(
    TokenizationJSONHelpers.getCollectionJSON(collectionId)
);
// Decode and check manager field

// For transfers, ensure approvals are set
// Check outgoing approval from sender
// Check incoming approval for recipient
```

---

### 7. Collection Archived

**Error:**
```
precompile error [code=8]: collection archived: cannot modify archived collection
```

**Cause:** Attempting to modify an archived collection.

**Solution:**
```solidity
// Check if collection is archived before operations
// If you're the manager and need to modify, unarchive first
string memory json = TokenizationJSONHelpers.setIsArchivedJSON(
    collectionId,
    false,  // unarchive
    "[]"    // canArchiveCollection permission
);
precompile.updateCollection(json);
```

---

### 8. Invalid Range (start > end)

**Error:**
```
precompile error [code=4]: invalid range: start 100 is greater than end 50
```

**Cause:** Range has start value greater than end value.

**Solution:**
```solidity
// Validate ranges before building JSON
require(startTime <= endTime, "Invalid time range");
require(startTokenId <= endTokenId, "Invalid token ID range");

string memory rangeJson = TokenizationJSONHelpers.uintRangeToJson(startTime, endTime);
```

---

### 9. EVM Query Challenge Failed

**Error:**
```
precompile error [code=9]: EVM query challenge failed: contract returned 0, expected >= 1
```

**Cause:** An EVM query challenge in the collection invariants failed.

**Debugging:**
1. Check what contract is being called
2. Verify the calldata is correct
3. Check the expected result and comparison operator
4. Ensure the target contract is deployed and working

**Solution:**
```solidity
// Test the EVM query manually before using in invariants
(bool success, bytes memory result) = targetContract.staticcall(calldata);
require(success, "EVM call failed");
// Decode result and compare with expected
```

---

### 10. Dynamic Store Not Found

**Error:**
```
precompile error [code=10]: dynamic store not found: store 999 does not exist
```

**Cause:** The dynamic store ID doesn't exist.

**Solution:**
```solidity
// Verify store exists
try precompile.getDynamicStore(TokenizationJSONHelpers.getDynamicStoreJSON(storeId)) {
    // Store exists
} catch {
    // Create the store first
    uint256 newStoreId = precompile.createDynamicStore(
        TokenizationJSONHelpers.createDynamicStoreJSON(false, "", "")
    );
}
```

---

### 11. Approval Not Found

**Error:**
```
precompile error [code=11]: approval not found: approval "my-approval" does not exist
```

**Cause:** Trying to delete or reference a non-existent approval.

**Solution:**
```solidity
// Check if approval exists before deleting
// Approvals are identified by approvalId string
```

---

### 12. Invalid Approval Criteria

**Error:**
```
precompile error [code=12]: invalid approval criteria: merkle root is required for merkle challenge
```

**Cause:** Approval criteria configuration is incomplete or invalid.

**Common Issues:**
- Merkle challenge without merkle root
- Vote criteria without proposal ID
- Amount tracker without proper limits

---

## Error Codes Reference

| Code | Name | Description |
|------|------|-------------|
| 1 | `InvalidInput` | Invalid input parameters |
| 2 | `JSONUnmarshal` | Failed to parse JSON |
| 3 | `InternalError` | Internal precompile error |
| 4 | `InvalidRange` | Range validation failed |
| 5 | `NotFound` | Resource not found (collection, store, etc.) |
| 6 | `InsufficientBalance` | Not enough tokens |
| 7 | `NotAuthorized` | Permission denied |
| 8 | `Archived` | Collection is archived |
| 9 | `EVMQueryFailed` | EVM query challenge failed |
| 10 | `StoreNotFound` | Dynamic store not found |
| 11 | `ApprovalNotFound` | Approval not found |
| 12 | `InvalidCriteria` | Invalid approval criteria |

---

## Debugging Tips

### 1. Log JSON Before Sending

```solidity
// Emit event with JSON for debugging (remove in production)
event DebugJSON(string json);

function debugTransfer(...) external {
    string memory json = TokenizationJSONHelpers.transferTokensJSON(...);
    emit DebugJSON(json);
    precompile.transferTokens(json);
}
```

### 2. Validate JSON Externally

Copy the JSON from logs and validate it:
- Use online JSON validators
- Check against proto message definition
- Ensure field names match exactly

### 3. Test Individual Components

```solidity
// Test range construction
string memory rangeJson = TokenizationJSONHelpers.uintRangeToJson(1, 100);
// Expected: [{"start":"1","end":"100"}]

// Test address conversion
string memory addrStr = TokenizationJSONHelpers.addressToString(msg.sender);
// Expected: 0x742d35cc6634c0532925a3b844bc9e7595f0beb
```

### 4. Check Precompile is Responding

```solidity
// Simple connectivity test
try precompile.params("{}") {
    // Precompile is responding
} catch {
    revert("Precompile not available");
}
```

### 5. Use Address Conversion for Debugging

```solidity
// Convert EVM address to bech32 for checking on Cosmos side
string memory bech32 = precompile.convertEvmAddressToBech32(msg.sender);
// Returns: bb1...
```

---

## JSON Format Quick Reference

| Field Type | JSON Format | Example |
|------------|-------------|---------|
| `uint256` | String | `"123456789"` |
| `uint64` | String | `"18446744073709551615"` |
| `bool` | Raw boolean | `true` or `false` |
| `address` | Hex string | `"0x742d35Cc..."` |
| `string` | Quoted string | `"hello world"` |
| `UintRange[]` | Object array | `[{"start":"1","end":"100"}]` |
| `string[]` | String array | `["a","b","c"]` |
| Empty object | `{}` | `{}` |
| Empty array | `[]` | `[]` |

---

## Getting Help

If you encounter an error not covered here:

1. Check the [API Reference](API.md) for correct method signatures
2. Review the [Security Best Practices](security.md)
3. Look at [Example Contracts](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts/examples)
4. Check the [precompile implementation](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/x/tokenization/precompile) for error definitions

# Error Handling

The precompile uses structured error handling. All methods can revert with descriptive error messages.

## Error Handling in Solidity

Handle errors using standard Solidity error handling:

```solidity
function safeTransfer(...) external {
    try precompile.transferTokens(...) returns (bool success) {
        require(success, "Transfer failed");
    } catch Error(string memory reason) {
        revert(reason);
    } catch {
        revert("Unknown error");
    }
}
```

## Common Errors and Solutions

### Range Value Overflow

**Error:**
```
precompile error [code=4]: transaction failed: invalid balance times: range at index 0 has end 115792089237316195423570985008687907853269984665640564039457584007913129639935 greater than max 18446744073709551615
```

**Cause:** Using `type(uint256).max` instead of `type(uint64).max` for ownership times or badge ID ranges.

**Solution:**
```solidity
// ❌ Wrong - uint256 max is too large
string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(1, type(uint256).max);

// ✅ Correct - use uint64 max
string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(1, type(uint64).max);
```

**Note:** The precompile stores timestamps and IDs as uint64 internally. The max value is `18446744073709551615`.

---

### Address Cannot Be Empty

**Error:**
```
precompile error [code=1]: invalid input parameters: address cannot be empty
```

**Cause:** Using wrong field name in JSON for dynamic store operations.

**Solution:**
```solidity
// ❌ Wrong - "userAddress" is not recognized
'{"storeId":"1","userAddress":"0x...","value":true}'

// ✅ Correct - use "address"
'{"storeId":"1","address":"0x...","value":true}'
```

Both `setDynamicStoreValue` and `getDynamicStoreValue` use `"address"` as the field name.

---

### Failed to Unmarshal JSON

**Error:**
```
failed to unmarshal JSON for method X: precompile error [code=...]
```

**Cause:** Invalid JSON format or missing required fields.

**Solution:**
- Verify JSON is valid (no trailing commas, proper quoting)
- Check all required fields are present
- Ensure numbers are strings in JSON (e.g., `"123"` not `123`)
- Ensure booleans are not strings (e.g., `true` not `"true"`)

**Example correct format:**
```json
{
  "storeId": "123",
  "address": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "value": true
}
```

---

## Common Error Scenarios

- **Invalid Input**: Check that all addresses are non-zero, ranges are valid (start <= end), and amounts are positive
- **Collection Not Found**: Verify the collection ID exists
- **Insufficient Balance**: Ensure the caller has sufficient balance for transfers
- **Unauthorized**: Check that approvals and permissions are configured correctly
- **Collection Archived**: Archived collections are read-only

## JSON Format Quick Reference

| Type | Format | Example |
|------|--------|---------|
| Numbers | String | `"123"` |
| Booleans | Raw | `true` or `false` |
| Addresses | Hex string | `"0x742d35..."` |
| Ranges | Object array | `[{"start":"1","end":"100"}]` |
| Max uint64 | String | `"18446744073709551615"` |

For complete error code definitions and detailed error handling, see the [precompile implementation](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/x/tokenization/precompile).

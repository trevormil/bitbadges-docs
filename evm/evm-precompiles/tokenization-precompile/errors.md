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

## Common Error Scenarios

- **Invalid Input**: Check that all addresses are non-zero, ranges are valid (start <= end), and amounts are positive
- **Collection Not Found**: Verify the collection ID exists
- **Insufficient Balance**: Ensure the caller has sufficient balance for transfers
- **Unauthorized**: Check that approvals and permissions are configured correctly
- **Collection Archived**: Archived collections are read-only

For complete error code definitions and detailed error handling, see the [precompile implementation](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/x/tokenization/precompile).

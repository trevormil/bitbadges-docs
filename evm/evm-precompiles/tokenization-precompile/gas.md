# Gas Costs

Gas costs are calculated dynamically based on operation complexity. More complex operations (multiple recipients, ranges, etc.) consume more gas.

For detailed gas calculations and optimization strategies, see the implementation in the [precompile code](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/x/tokenization/precompile).

## General Principles

- **Base costs** apply to all operations
- **Per-element costs** for arrays (recipients, ranges, etc.)
- **Query operations** are generally cheaper than transactions
- **Batch operations** are more gas-efficient than multiple individual calls

## Optimization Tips

- Minimize the number of recipients when transferring
- Consolidate token ID and ownership time ranges when possible
- Use direct return methods (`getBalanceAmount`, `getTotalSupply`) instead of protobuf-encoded methods when you only need amounts
- Batch multiple operations in a single transaction when possible

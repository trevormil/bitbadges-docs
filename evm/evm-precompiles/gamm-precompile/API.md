# GAMM Precompile API Reference

**Address:** `0x0000000000000000000000000000000000001002`

## Interface

```solidity
interface IGammPrecompile {
    // Transaction Methods
    function joinPool(string calldata msgJson) external returns (uint256 shareOutAmount, Coin[] memory tokenIn);
    function exitPool(string calldata msgJson) external returns (Coin[] memory tokenOut);
    function swapExactAmountIn(string calldata msgJson) external returns (uint256 tokenOutAmount);
    function swapExactAmountInWithIBCTransfer(string calldata msgJson) external returns (uint256 tokenOutAmount);
    function createPool(string calldata msgJson) external returns (uint256 poolId);

    // Query Methods
    function getPool(string calldata msgJson) external view returns (bytes memory);
    function getPools(string calldata msgJson) external view returns (bytes memory);
    function getPoolType(string calldata msgJson) external view returns (string memory);
    function calcJoinPoolNoSwapShares(string calldata msgJson) external view returns (Coin[] memory tokensOut, uint256 sharesOut);
    function calcExitPoolCoinsFromShares(string calldata msgJson) external view returns (Coin[] memory tokensOut);
    function calcJoinPoolShares(string calldata msgJson) external view returns (uint256 shareOutAmount, Coin[] memory tokensOut);
    function getPoolParams(string calldata msgJson) external view returns (bytes memory);
    function getTotalShares(string calldata msgJson) external view returns (Coin memory totalShares);
    function getTotalLiquidity(string calldata msgJson) external view returns (Coin[] memory liquidity);
}

struct Coin {
    string denom;
    uint256 amount;
}
```

## JSON Formats

### joinPool

```json
{
  "poolId": "1",
  "shareOutAmount": "1000000",
  "tokenInMaxs": [{"denom": "ubadge", "amount": "1000000000"}]
}
```

### exitPool

```json
{
  "poolId": "1",
  "shareInAmount": "1000000",
  "tokenOutMins": [{"denom": "ubadge", "amount": "0"}]
}
```

### swapExactAmountIn

```json
{
  "routes": [{"poolId": "1", "tokenOutDenom": "uatom"}],
  "tokenIn": {"denom": "ubadge", "amount": "1000000000"},
  "tokenOutMinAmount": "900000000",
  "affiliates": []
}
```

### swapExactAmountInWithIBCTransfer

Same as `swapExactAmountIn` plus:

```json
{
  "ibcTransferInfo": {
    "sourceChannel": "channel-0",
    "receiver": "cosmos1...",
    "memo": "",
    "timeoutTimestamp": "1234567890"
  }
}
```

### createPool (Balancer)

```json
{
  "poolParams": {"swapFee": "0.003", "exitFee": "0"},
  "poolAssets": [
    {"token": {"denom": "ubadge", "amount": "1000000"}, "weight": "1"},
    {"token": {"denom": "uatom", "amount": "1000000"}, "weight": "1"}
  ]
}
```

### Query Methods

All query methods use `{"poolId": "1"}` format or similar simple JSON.

## Gas Costs

| Method | Base Gas | Dynamic Costs |
|--------|----------|---------------|
| `joinPool` | 210,000 | +2,000 per coin |
| `exitPool` | 210,000 | +2,000 per coin |
| `swapExactAmountIn` | 210,000 | +5,000 per route |
| `createPool` | 215,000 | +3,000 per asset |
| Queries | 52,000-55,000 | - |

## Error Codes

| Code | Name | Description |
|------|------|-------------|
| 1 | InvalidInput | Invalid input parameters |
| 2 | PoolNotFound | Pool not found |
| 3 | SwapFailed | Swap operation failed |
| 4 | QueryFailed | Query failed |
| 5 | InternalError | Internal error |
| 6 | Unauthorized | Unauthorized |
| 7 | JoinPoolFailed | Join pool failed |
| 8 | ExitPoolFailed | Exit pool failed |
| 9 | IBCTransferFailed | IBC transfer failed |

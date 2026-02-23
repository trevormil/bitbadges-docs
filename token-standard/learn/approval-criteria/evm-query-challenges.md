# EVM Query Challenges

EVM Query Challenges allow approvals to be gated by read-only EVM contract queries. The same challenge structure is also used in **invariants** (`invariants.evmQueryChallenges`), which run after every transfer and are typically used for supply control, balance caps, or max-holder checks rather than per-transfer approval gating.

- **Approval criteria:** `approvalCriteria.evmQueryChallenges` — checked before a transfer is allowed; placeholders: `$initiator`, `$sender`, `$recipient`, `$collectionId`.
- **Invariants:** `invariants.evmQueryChallenges` — checked after all balance updates; same placeholders plus `$recipients` (all recipients as concatenated 32-byte hex). See [Collection setup – invariants](../collection-setup-fields.md#invariants) for where invariants are configured.

The challenge executes a `staticcall` to the specified contract with the given calldata. The result is compared against the expected result using the specified comparison operator. All challenges must pass for the transfer to be approved (or for the invariant to pass).

## Structure

### Challenge Fields

| Field                | Type   | Description                                                                                  |
| -------------------- | ------ | -------------------------------------------------------------------------------------------- |
| `contractAddress`    | string | EVM contract address to query (0x format or bb1 format)                                      |
| `calldata`           | string | ABI-encoded function selector + arguments (hex string without 0x prefix)                     |
| `expectedResult`     | string | Expected return value (hex string without 0x prefix). If empty, any non-error result passes. |
| `comparisonOperator` | string | How to compare: `eq`, `ne`, `gt`, `gte`, `lt`, `lte`. Default is `eq`.                       |
| `gasLimit`           | string | Gas limit for the query (default 100000, max 500000)                                         |
| `uri`                | string | Optional metadata URI                                                                        |
| `customData`         | string | Optional custom data                                                                         |

## Placeholders

The `calldata` field supports dynamic placeholders that are replaced at runtime. **Placeholders differ between approval criteria and invariants** because approval checks run per (from, to) pair while invariants run once after all transfers and can see multiple recipients.

### Approval criteria (transfer gating)

Used in `approvalCriteria.evmQueryChallenges` on collection or user approvals. One recipient per check.

| Placeholder     | Description                              |
| --------------- | ---------------------------------------- |
| `$initiator`    | Address of the transfer initiator        |
| `$sender`       | Address sending the tokens (from)       |
| `$recipient`    | Address receiving the tokens (to)        |
| `$collectionId` | Collection ID (uint256, 32-byte hex)     |

**Not available in approval context:** `$recipients` (approval runs per single recipient).

### Invariants (post-transfer)

Used in `invariants.evmQueryChallenges`. Checked once after all balance updates; can reference multiple recipients.

| Placeholder     | Description                                                                 |
| --------------- | --------------------------------------------------------------------------- |
| `$initiator`    | Address of the transfer initiator                                          |
| `$sender`       | Address sending the tokens (from)                                          |
| `$recipient`    | First recipient only (convenience for single-recipient transfers)          |
| `$recipients`   | All recipient addresses concatenated as 32-byte-padded hex (no separator)  |
| `$collectionId` | Collection ID (uint256, 32-byte hex)                                       |

**Note:** `$recipients` is each address ABI-padded to 32 bytes then concatenated (e.g. for two recipients, 64 hex chars + 64 hex chars). It is not comma-separated.

**Example with placeholder:**

```json
{
    "evmQueryChallenges": [
        {
            "contractAddress": "0x1234567890123456789012345678901234567890",
            "calldata": "70a08231000000000000000000000000$initiator",
            "expectedResult": "0000000000000000000000000000000000000000000000000000000000000001",
            "comparisonOperator": "gte",
            "gasLimit": "100000"
        }
    ]
}
```

This checks that the initiator has at least 1 token balance in the ERC-20 contract.

## Comparison Operators

| Operator | Description              | Use Case                              |
| -------- | ------------------------ | ------------------------------------- |
| `eq`     | Equals                   | Exact value matching                  |
| `ne`     | Not equals               | Exclusion checks                      |
| `gt`     | Greater than             | Minimum balance/value requirements    |
| `gte`    | Greater than or equal    | Minimum threshold checks              |
| `lt`     | Less than                | Maximum limit checks                  |
| `lte`    | Less than or equal       | Maximum threshold checks              |

**Note:** Only `eq` and `ne` work reliably for non-numeric return types.

## Query Execution

1. **Placeholder Replacement**: All placeholders in `calldata` are replaced with actual addresses
2. **Static Call**: Execute `eth_call` (staticcall) to the contract with the calldata
3. **Gas Limit**: Query is limited by the specified `gasLimit` to prevent DoS attacks
4. **Result Comparison**: Compare the returned value against `expectedResult` using `comparisonOperator`
5. **Pass/Fail**: If comparison succeeds, challenge passes; otherwise, transfer is rejected

## Type Definitions

```typescript
interface EVMQueryChallenge {
    contractAddress: string;     // EVM contract address to query
    calldata: string;            // ABI-encoded function call (hex without 0x)
    expectedResult?: string;     // Expected return value (hex without 0x)
    comparisonOperator?: string; // eq, ne, gt, gte, lt, lte (default: eq)
    gasLimit: string;            // Gas limit for query (default 100000, max 500000)
    uri?: string;                // Optional metadata URI
    customData?: string;         // Optional custom data
}
```

## Use Cases

### ERC-20 Balance Check

Require the sender to hold at least 100 tokens of an ERC-20:

```json
{
    "evmQueryChallenges": [
        {
            "contractAddress": "0xUSDCAddress...",
            "calldata": "70a08231000000000000000000000000$sender",
            "expectedResult": "0000000000000000000000000000000000000000000000000000000000000064",
            "comparisonOperator": "gte",
            "gasLimit": "100000"
        }
    ]
}
```

The `70a08231` is the function selector for `balanceOf(address)`.

### NFT Ownership Verification

Require the initiator to own a specific NFT:

```json
{
    "evmQueryChallenges": [
        {
            "contractAddress": "0xNFTContract...",
            "calldata": "6352211e0000000000000000000000000000000000000000000000000000000000000001",
            "expectedResult": "$initiator",
            "comparisonOperator": "eq",
            "gasLimit": "100000"
        }
    ]
}
```

The `6352211e` is the function selector for `ownerOf(uint256)`.

## Building Calldata

To build the calldata for a function call:

1. **Get Function Selector**: First 4 bytes of `keccak256(functionSignature)`
2. **Encode Parameters**: ABI-encode the function parameters
3. **Concatenate**: Combine selector + encoded parameters
4. **Add Placeholders**: Replace address parameters with placeholders as needed

**Example for `balanceOf(address)`:**

1. Function signature: `balanceOf(address)`
2. Selector: `keccak256("balanceOf(address)")` → `70a08231`
3. Address parameter: Pad to 32 bytes → `000000000000000000000000<address>`
4. With placeholder: `70a08231000000000000000000000000$initiator`

## Gas Limits

| Query Type      | Recommended Gas |
| --------------- | --------------- |
| Simple storage  | 30,000          |
| Balance check   | 50,000          |
| Complex logic   | 100,000         |
| Multiple calls  | 200,000         |
| Maximum allowed | 500,000         |

## Error Conditions

The challenge fails if:

- Contract address is invalid or doesn't exist
- Calldata is malformed or empty
- Contract reverts during the call
- Query exceeds gas limit
- Return value doesn't match expected result
- Comparison operator is invalid
- Result cannot be compared (e.g., numeric comparison on non-numeric data)

## Security Considerations

### Read-Only Execution

EVM Query Challenges execute as `staticcall`, which:
- Cannot modify state
- Cannot emit events
- Cannot create or destroy contracts
- Is fully deterministic within a block

### Gas Limit Protection

Set appropriate gas limits to prevent:
- DoS attacks through expensive queries
- Excessive resource consumption
- Unpredictable execution costs

### Contract Trust

Only query trusted contracts:
- Malicious contracts could return misleading data
- Ensure the contract's logic is verified
- Consider upgrade risks for proxy contracts

### Determinism

All queries are deterministic within a block because:
- EVM state is consistent within a block
- `staticcall` cannot modify state
- Results are reproducible

### Placeholder Security

Placeholders are replaced at runtime:
- Cannot be manipulated by users
- Values come from the transfer context
- Prevents injection attacks

## Best Practices

1. **Use verified contracts**: Only query well-audited contracts
2. **Set appropriate gas limits**: Balance between reliability and cost
3. **Test thoroughly**: Verify calldata produces expected results
4. **Document requirements**: Use `uri` to explain what the challenge verifies
5. **Handle edge cases**: Consider what happens with zero balances, non-existent tokens, etc.
6. **Combine with other challenges**: Use alongside merkle challenges, voting, etc. for defense in depth

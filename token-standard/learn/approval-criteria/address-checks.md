# Address Checks

Additional restrictions on address types for transfer approval. These checks validate whether addresses involved in a transfer meet specific requirements (e.g., must be a WASM contract, must not be a liquidity pool).

## Interface

```typescript
interface AddressChecks {
    /** Require the address to be a WASM contract. */
    mustBeWasmContract?: boolean;
    /** Require the address to not be a WASM contract. */
    mustNotBeWasmContract?: boolean;
    /** Require the address to be a liquidity pool. */
    mustBeLiquidityPool?: boolean;
    /** Require the address to not be a liquidity pool. */
    mustNotBeLiquidityPool?: boolean;
}
```

## How It Works

Address checks validate the type of addresses involved in a transfer. The checks are applied to different parties depending on the approval type:

### Collection Approvals

Collection approvals can check all three parties:

-   **`senderChecks`**: Validates the sender address (`from`)
-   **`recipientChecks`**: Validates the recipient address (`to`)
-   **`initiatorChecks`**: Validates the initiator address (`initiatedBy`)

### Incoming Approvals

Incoming approvals can check the sender and initiator (but not the recipient, since the recipient is always the approval owner):

-   **`senderChecks`**: Validates the sender address (`from`)
-   **`initiatorChecks`**: Validates the initiator address (`initiatedBy`)

### Outgoing Approvals

Outgoing approvals can check the recipient and initiator (but not the sender, since the sender is always the approval owner):

-   **`recipientChecks`**: Validates the recipient address (`to`)
-   **`initiatorChecks`**: Validates the initiator address (`initiatedBy`)

## Available Checks

### WASM Contract Checks

-   **`mustBeWasmContract`**: Requires the address to be a CosmWASM smart contract
-   **`mustNotBeWasmContract`**: Requires the address to NOT be a CosmWASM smart contract

### Liquidity Pool Checks

-   **`mustBeLiquidityPool`**: Requires the address to be a liquidity pool
-   **`mustNotBeLiquidityPool`**: Requires the address to NOT be a liquidity pool

## Examples

### Collection Approval: Require Contract Recipients

Only allow transfers to WASM contracts:

```json
{
    "approvalCriteria": {
        "recipientChecks": {
            "mustBeWasmContract": true
        }
    }
}
```

### Collection Approval: Block Liquidity Pool Senders

Prevent transfers from liquidity pools:

```json
{
    "approvalCriteria": {
        "senderChecks": {
            "mustNotBeLiquidityPool": true
        }
    }
}
```

### Incoming Approval: Require Contract Initiators

Only allow transfers initiated by WASM contracts:

```json
{
    "approvalCriteria": {
        "initiatorChecks": {
            "mustBeWasmContract": true
        }
    }
}
```

### Outgoing Approval: Block Liquidity Pool Recipients

Prevent sending tokens to liquidity pools:

```json
{
    "approvalCriteria": {
        "recipientChecks": {
            "mustNotBeLiquidityPool": true
        }
    }
}
```

### Combined Checks

You can combine multiple checks for a single party:

```json
{
    "approvalCriteria": {
        "recipientChecks": {
            "mustBeWasmContract": true,
            "mustNotBeLiquidityPool": true
        },
        "initiatorChecks": {
            "mustNotBeWasmContract": true
        }
    }
}
```

## Use Cases

-   **Smart Contract Integration**: Require transfers to/from specific contract types
-   **Liquidity Protection**: Prevent transfers to/from liquidity pools to maintain token economics
-   **Security**: Enforce that certain operations can only be initiated by contracts or regular addresses
-   **Protocol Compliance**: Ensure transfers comply with protocol-specific address requirements

## Constraints

All checks are evaluated after the address lists (`toList`, `fromList`, `initiatedByList`) are matched. An address must first be in the appropriate list, then pass the address checks.

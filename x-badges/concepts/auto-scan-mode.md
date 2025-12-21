# Auto-Scan Mode

Auto-scan mode is a feature that allows the BitBadges system to automatically scan through available approvals to find a match for a transfer without requiring explicit prioritization. This mode provides convenience and efficiency for automatic environments, such as liquidity pools and other automated environments.

## Overview

When a transfer is submitted without explicitly prioritizing specific approvals for a specific approval level, the system operates in **auto-scan mode**. In this mode, the system automatically searches through available approvals to find a match for the transfer request. It will break down into partial if needed and continue until the transfer is fully approved or denied.

However, not all approvals are safe for auto-scanning. Some approvals have side effects or require specific handling that makes automatic selection unsafe. The system checks whether an approval is auto-scannable before using it in this mode.

## Safety Check Logic

An approval is considered **safe for auto-scan mode** if:

### 1. No Approval Criteria

If `approvalCriteria` is `nil` or empty, the approval is automatically safe for auto-scanning.

```go
if approvalCriteria == nil {
    return true
}
```

### 2. Must Prioritize Flag

If `mustPrioritize` is set to `true`, the approval **cannot** be auto-scanned. This flag explicitly requires the approval to be prioritized in the transfer request.

```go
if approvalCriteria.MustPrioritize {
    return false
}
```

**Why**: Approvals with `mustPrioritize` set are intentionally designed to require explicit selection, preventing accidental use.

### 3. Coin Transfers

If the approval includes any coin transfers (BADGE or other sdk.Coin transfers), it is **not** auto-scannable.

```go
if approvalCriteria.CoinTransfers != nil && len(approvalCriteria.CoinTransfers) > 0 {
    return false
}
```

**Why**: Coin transfers execute side effects (payments) that should be explicitly intended by the user. Auto-selecting an approval with coin transfers could result in unexpected payments.

### 4. Predetermined Balances

If the approval uses predetermined balances (non-nil and non-empty), it is **not** auto-scannable.

```go
if approvalCriteria.PredeterminedBalances != nil && !PredeterminedBalancesIsBasicallyNil(approvalCriteria.PredeterminedBalances) {
    return false
}
```

**Why**: Predetermined balances specify exact amounts that must be transferred, which could affect which token IDs are received. This requires explicit approval selection to ensure the user understands what they're receiving.

### 5. Merkle Challenges

If the approval includes merkle challenges, it is **not** auto-scannable.

```go
if approvalCriteria.MerkleChallenges != nil && len(approvalCriteria.MerkleChallenges) > 0 {
    return false
}
```

**Why**: Merkle challenges require proofs to be passed in `MsgTransferTokens`. The system cannot automatically select an approval that requires specific proof data that must be provided by the user.

### 6. ETH Signature Challenges

If the approval includes ETH signature challenges, it is **not** auto-scannable.

```go
if approvalCriteria.EthSignatureChallenges != nil && len(approvalCriteria.EthSignatureChallenges) > 0 {
    return false
}
```

**Why**: Similar to merkle challenges, ETH signature challenges require specific signature data to be provided in the transfer message, which cannot be automatically selected.

## Safe Criteria for Auto-Scan

The following approval criteria are **safe** for auto-scan mode and will not prevent an approval from being auto-scannable:

-   **Read-only checks**: `mustOwnTokens`, address checks (`senderChecks`, `recipientChecks`, `initiatorChecks`), and other read-only validations
-   **Requires flags**: `requireToEqualsInitiatedBy`, `requireFromEqualsInitiatedBy`, etc.
-   **Overrides**: `overridesFromOutgoingApprovals`, `overridesToIncomingApprovals`
-   **Auto-deletion options**: `autoDeletionOptions`
-   **User royalties**: `userRoyalties` (read-only during transfer)
-   **Alt time checks**: `altTimeChecks` (read-only validation)

These criteria only perform read-only checks or affect approval behavior without requiring additional data in the transfer message.

## Designing Approvals Properly

It is crucial to design your approvals with auto-scan mode in mind. While the system automatically prevents certain types of approvals from being auto-scanned, there are important design considerations that require explicit attention.

### The `mustPrioritize` Flag

The `mustPrioritize` flag is a powerful tool for controlling whether an approval can be used in auto-scan mode. Setting `mustPrioritize: true` ensures that the approval **must** be explicitly prioritized in the transfer request, preventing it from being automatically selected.

**Consider using `mustPrioritize: true` when:**

-   **Forceful transfers**: Approvals that override user-level approvals (`overridesFromOutgoingApprovals` or `overridesToIncomingApprovals`) should typically require explicit prioritization, as these are important operations that should be intentional. While overrides are technically safe for auto-scanning, they bypass user consent and should be used intentionally, not automatically.

-   **Stateful approvals**: Approvals that increment stateful trackers like `approvalAmounts` or `maxNumTransfers` should often require prioritization. Users may not want to unintentionally increment their transfer limits, especially in automated environments like liquidity pools where they cannot control which approval is selected.

-   **Sensitive operations**: Any approval that performs operations users might want explicit control over, even if technically safe for auto-scanning.

## Complete Function Reference

```go
func CollectionApprovalIsAutoScannable(approvalCriteria *ApprovalCriteria) bool {
	if approvalCriteria == nil {
		return true
	}

	if approvalCriteria.MustPrioritize {
		return false
	}

	if approvalCriteria.CoinTransfers != nil && len(approvalCriteria.CoinTransfers) > 0 {
		return false
	}

	if approvalCriteria.PredeterminedBalances != nil && !PredeterminedBalancesIsBasicallyNil(approvalCriteria.PredeterminedBalances) {
		return false
	}

	if approvalCriteria.MerkleChallenges != nil && len(approvalCriteria.MerkleChallenges) > 0 {
		return false
	}

	if approvalCriteria.EthSignatureChallenges != nil && len(approvalCriteria.EthSignatureChallenges) > 0 {
		return false
	}

	return true
}
```

## Usage Guidelines

### When to Use Auto-Scan Mode

Auto-scan mode is ideal for:

-   **Simple transfers**: Basic token transfers without side effects
-   **Standard approvals**: Approvals with empty or minimal approval criteria
-   **User convenience**: When you want the system to automatically find the right approval

### When to Use Prioritized Approvals

You **must** use prioritized approvals when:

-   The approval has `mustPrioritize` set to `true`
-   The approval includes coin transfers
-   The approval uses predetermined balances
-   The approval requires merkle proofs
-   The approval requires ETH signatures
-   You need explicit control over which approval is used

### Special Contexts Requiring Prioritized Approvals

Certain special transfer contexts are also **not compatible with auto-scan mode** and require you to specify prioritized collection approvals:

#### IBC Backed Minting Paths

When using [IBC backed paths](./ibc-backed-paths.md) for minting/unwinding tokens, transfers involving the special backing address **cannot** use auto-scan mode. We enforce that you must explicitly specify the prioritized collection approval in your transfer request.

#### Cosmos Wrapper Paths

When using [Cosmos wrapper paths](./cosmos-wrapper-paths.md) for wrapping/unwrapping tokens to native Cosmos SDK coins, transfers involving wrapper addresses **cannot** use auto-scan mode. We enforce that you must explicitly specify the prioritized collection approval in your transfer request.

See [Transferability & Approvals](./transferability-approvals.md) for more details on prioritized approvals.

## Examples

### ✅ Auto-Scannable Approval

```json
{
    "approvalId": "simple-transfer",
    "fromListId": "Mint",
    "toListId": "All",
    "initiatedByListId": "All",
    "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "tokenIds": [{ "start": "1", "end": "100" }],
    "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "approvalCriteria": {
        "mustOwnTokens": [
            {
                "tokenIds": [{ "start": "1", "end": "1" }],
                "ownershipTimes": [
                    { "start": "1", "end": "18446744073709551615" }
                ]
            }
        ]
    }
}
```

This approval is auto-scannable because it only includes read-only checks (`mustOwnTokens`).

### ❌ Not Auto-Scannable - Coin Transfers

```json
{
    "approvalId": "paid-transfer",
    "approvalCriteria": {
        "coinTransfers": [
            {
                "toAddress": "bb1...",
                "amount": "1000000",
                "denom": "badge"
            }
        ]
    }
}
```

This approval is **not** auto-scannable because it includes coin transfers that execute payments.

### ❌ Not Auto-Scannable - Must Prioritize

```json
{
    "approvalId": "priority-approval",
    "approvalCriteria": {
        "mustPrioritize": true
    }
}
```

This approval is **not** auto-scannable because it explicitly requires prioritization.

### ❌ Not Auto-Scannable - Merkle Challenges

```json
{
    "approvalId": "merkle-approval",
    "approvalCriteria": {
        "merkleChallenges": [
            {
                "root": "0x...",
                "expectedProofLength": 3
            }
        ]
    }
}
```

This approval is **not** auto-scannable because it requires merkle proofs to be provided in the transfer message.

## Related Topics

-   [Transferability & Approvals](./transferability-approvals.md) - Overview of the approval system and auto-scan vs prioritized modes
-   [Approval Criteria](./approval-criteria/) - Detailed explanation of approval criteria components
-   [Empty Approval Criteria](../examples/empty-approval-criteria.md) - Template for auto-scannable approvals

# Must Prioritize

Control whether an approval requires explicit prioritization in transfer requests or can be used in auto-scan mode.

## Interface

```typescript
interface ApprovalCriteria<T extends NumberType> {
    mustPrioritize?: boolean;
}
```

## How It Works

The `mustPrioritize` field determines how an approval can be used during transfer evaluation:

### `mustPrioritize: true` (Required Prioritization)

When set to `true`, the approval **must** be explicitly specified in the `prioritizedApprovals` field of `MsgTransferTokens`. The transfer will fail if:

-   The approval is not included in `prioritizedApprovals`
-   The specified version doesn't match the current approval version
-   The approval doesn't match the transfer criteria

### `mustPrioritize: false` or `undefined` (Auto-Scan Compatible)

When set to `false` or not specified, the approval can be used in **auto-scan mode** if other auto-scan criteria are met:

-   The approval has [Empty Approval Criteria](../examples/empty-approval-criteria.md) (no side effects)
-   No custom logic or side effects are present
-   The transfer doesn't require explicit version control

## Use Cases

### Requiring Explicit Prioritization

Use `mustPrioritize: true` when you need:

-   **Version Control**: Ensure users are aware of the exact approval version they're using
-   **Side Effects**: Approvals with coin transfers, custom logic, or other side effects
-   **Deterministic Behavior**: Guarantee which approval will be used for the transfer
-   **Security**: Prevent accidental use of the wrong approval

### Allowing Auto-Scan

Use `mustPrioritize: false` (or omit) when:

-   **Simple Transfers**: No side effects or custom logic
-   **User Convenience**: Allow automatic approval selection
-   **Empty Criteria**: Using [Empty Approval Criteria](../examples/empty-approval-criteria.md)

## Examples

### Required Prioritization

An approval that requires explicit prioritization:

```json
{
    "approvalCriteria": {
        "mustPrioritize": true,
        "coinTransfers": [
            {
                "coins": [
                    {
                        "denom": "ubadge",
                        "amount": "1000000"
                    }
                ],
                "toAddress": "bb1..."
            }
        ]
    }
}
```

**Transfer must include**:

```typescript
{
    "prioritizedApprovals": [
        {
            "approvalId": "reward-approval",
            "approvalLevel": "collection",
            "approverAddress": "",
            "version": "1" // Must match current version
        }
    ],
    "onlyCheckPrioritizedCollectionApprovals": true
}
```

### Auto-Scan Compatible

An approval that can be auto-scanned:

```json
{
    "approvalCriteria": {
        "mustPrioritize": false
        // ... empty approval criteria with no side effects
    }
}
```

**Transfer can use auto-scan**:

```typescript
{
    "prioritizedApprovals": [], // Empty - will auto-scan
    // System will automatically find and use matching approval
}
```

## Relationship to Auto-Scan Mode

The `mustPrioritize` field works in conjunction with the auto-scan system:

### Auto-Scan Mode

-   **Default behavior**: System automatically scans approvals
-   **Works with**: Approvals where `mustPrioritize: false` or `undefined`
-   **Requires**: [Empty Approval Criteria](../examples/empty-approval-criteria.md) (no side effects)
-   **No versioning**: System handles approval selection automatically

### Prioritized Mode

-   **Explicit selection**: User must specify approvals in `prioritizedApprovals`
-   **Required for**: Approvals where `mustPrioritize: true`
-   **Version control**: Must specify exact approval version
-   **Deterministic**: Guarantees which approval will be used

## Important Notes

-   **Default Behavior**: If `mustPrioritize` is not specified, it defaults to `false` (auto-scan compatible)
-   **Side Effects**: Approvals with side effects (coin transfers, custom logic) should typically use `mustPrioritize: true`
-   **Race Conditions**: Using `mustPrioritize: true` with version control prevents race conditions from approval updates
-   **Transfer Flags**: When using prioritized approvals, set `onlyCheckPrioritizedCollectionApprovals` (or corresponding flags) to `true` for deterministic behavior

## Related Concepts

-   [Approval Criteria Overview](README.md)
-   [MsgTransferTokens](../../messages/msg-transfer-tokens.md) - Transfer message with prioritizedApprovals
-   [Auto-Scan vs Prioritized Approvals](../../concepts/transferability-approvals.md#auto-scan-vs-prioritized-approvals) - Transfer modes
-   [Empty Approval Criteria](../examples/empty-approval-criteria.md) - Auto-scan compatible template

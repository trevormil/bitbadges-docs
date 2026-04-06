# User Approval Settings

User approval settings let collection-level approvals control which denominations users can transfer, whether user-level coin transfers are disabled, and royalty configuration. These settings are **collection-level only** and are propagated to user-level approvals during greedy transfer matching.

## Interface

```typescript
interface UserApprovalSettings<T extends NumberType> {
    /** Restricts which coin denominations users can transfer via coinTransfers. */
    allowedDenoms?: string[];
    /** If true, user-level coinTransfers are completely disabled for this approval. */
    disableUserCoinTransfers?: boolean;
    /** Royalty configuration: percentage (basis points) and payout address. */
    userRoyalties?: iUserRoyalties<T>;
}
```

## Fields

### allowedDenoms

An array of denomination strings (e.g., `["ubadge", "ustake"]`) that restricts which coins users can include in `coinTransfers` at the user level. If set, any user-level coin transfer using a denomination not in the list will be rejected.

### disableUserCoinTransfers

When set to `true`, user-level `coinTransfers` are entirely disabled for approvals matching this criteria. Users cannot attach any coin transfers to their approvals.

### userRoyalties

Royalty configuration migrated from the previous separate `userRoyalties` field on `ApprovalCriteria`. Specifies a percentage (in basis points, where 10000 = 100%) and a payout address. When a transfer matches this approval, the specified percentage of the transferred coins is routed to the payout address.

## Availability

This field is **collection-level only**. It is not available on user-level (incoming/outgoing) approvals. The settings are propagated downward to user-level approvals during the greedy transfer matching process.

## Conflict Resolution

When a transfer matches multiple collection-level approvals that each define `userApprovalSettings`, all matched settings must be consistent. If the settings conflict (for example, one approval allows denom `"ubadge"` while another restricts it), the transfer is **rejected**. This prevents ambiguous or contradictory configurations from silently passing.

## Mutual Exclusivity

`allowedDenoms` and `disableUserCoinTransfers` are **mutually exclusive**. Setting both on the same approval will fail `validateBasic` validation. Use one or the other:

-   Use `allowedDenoms` to restrict which denominations are permitted
-   Use `disableUserCoinTransfers` to block all user coin transfers entirely

## Examples

### Restrict User Transfers to a Single Denomination

Only allow users to attach BADGE coin transfers:

```json
{
    "approvalCriteria": {
        "userApprovalSettings": {
            "allowedDenoms": ["ubadge"]
        }
    }
}
```

### Disable All User Coin Transfers

Prevent users from attaching any coin transfers to their approvals:

```json
{
    "approvalCriteria": {
        "userApprovalSettings": {
            "disableUserCoinTransfers": true
        }
    }
}
```

### Configure Royalties

Route 2.5% (250 basis points) of transferred coins to a royalty address:

```json
{
    "approvalCriteria": {
        "userApprovalSettings": {
            "userRoyalties": {
                "percentage": "250",
                "payoutAddress": "bb1royalty..."
            }
        }
    }
}
```

### Combined: Allow Only BADGE with Royalties

```json
{
    "approvalCriteria": {
        "userApprovalSettings": {
            "allowedDenoms": ["ubadge"],
            "userRoyalties": {
                "percentage": "500",
                "payoutAddress": "bb1creator..."
            }
        }
    }
}
```

This allows only `ubadge` coin transfers at the user level and routes 5% to the creator address.

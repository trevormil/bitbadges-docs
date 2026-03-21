# Build Recurring Payments and Subscriptions with No Smart Contract

This guide shows how to build a recurring payments protocol on BitBadges using native approval fields. No custom smart contract, no keeper bot, no gas sponsorship -- just two approvals with a handful of fields.

## The Problem

On-chain subscriptions today typically rely on unlimited token approvals. A subscriber grants open-ended access to their wallet, and a separate contract handles pulling funds on a schedule. This requires custom Solidity, a keeper service to trigger payments, a registry to track subscribers, and gas sponsorship for automated pulls.

BitBadges handles all of this at the protocol level.

## How It Works

A recurring payment on BitBadges is a pair of approvals:

1. **Collection-level approval** -- the merchant configures an approval allowing them to pull a fixed amount from subscribers on a fixed schedule.
2. **User-level outgoing approval** -- the subscriber authorizes the merchant to pull from their balance, with cancellation built in.

The chain tracks cumulative pull amounts per time interval. When a new interval starts, the tracker resets automatically.

## Step 1: Collection-Level Approval (Merchant Side)

The merchant creates an approval that allows pulling up to a fixed amount per interval.

```json
{
  "approvalId": "subscription-pull",
  "fromListId": "All",
  "toListId": "merchantAddress",
  "initiatedByListId": "merchantAddress",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "approvalAmounts": {
      "perFromAddressApprovalAmount": "10"
    },
    "maxNumTransfers": {
      "perFromAddressMaxNumTransfers": "1"
    },
    "resetTimeIntervals": {
      "startTime": "1700000000000",
      "intervalLength": "2592000000"
    }
  }
}
```

Key fields:

- **`perFromAddressApprovalAmount`** -- maximum amount the merchant can pull from each subscriber per interval (e.g., 10 tokens).
- **`resetTimeIntervals`** -- defines when the allowance resets. An `intervalLength` of `2592000000` (30 days in milliseconds) creates a monthly billing cycle.
- **`perFromAddressMaxNumTransfers`** -- limits the merchant to one pull per subscriber per interval.

## Step 2: User-Level Outgoing Approval (Subscriber Side)

The subscriber creates an outgoing approval authorizing the merchant to pull tokens.

```json
{
  "approvalId": "allow-merchant-pull",
  "toListId": "merchantAddress",
  "initiatedByListId": "merchantAddress",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "requireToEqualsInitiatedBy": true,
    "autoDeletionOptions": {
      "allowCounterpartyPurge": true
    }
  }
}
```

Key fields:

- **`requireToEqualsInitiatedBy`** -- the merchant can only pull tokens to their own address (not redirect them elsewhere).
- **`allowCounterpartyPurge`** -- the subscriber can revoke the approval at any time by purging it. This is the "cancel subscription" mechanism.

## Step 3: Cancellation

To cancel, the subscriber sends a `MsgPurgeApprovals` transaction that removes the outgoing approval. No waiting period, no penalty -- the approval is deleted immediately and the merchant can no longer pull tokens.

## Optional Enhancements

These fields compose with the base subscription approval. No contract upgrades needed.

### Auto-Expire After N Periods

Set `maxNumTransfers.overallMaxNumTransfers` to limit the total number of pulls. For example, set it to `12` for a 12-month subscription that ends automatically.

### Require Membership Badge

Add `mustOwnBadges` to the approval criteria to require subscribers to hold a specific membership badge before the merchant can pull:

```json
"mustOwnBadges": [{
  "collectionId": "5",
  "amountRange": { "start": "1", "end": "1" },
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "badgeIds": [{ "start": "1", "end": "1" }]
}]
```

### Tiered Pricing

Create multiple approvals with different `perFromAddressApprovalAmount` values and gate each tier with a different `mustOwnBadges` requirement. Basic-tier subscribers hold badge ID 1, premium-tier subscribers hold badge ID 2, and each tier has its own pull limit.

## Comparison

| Aspect | EVM (Custom Contract) | BitBadges (Native Approvals) |
|--------|----------------------|------------------------------|
| Code required | ~800 lines of Solidity + keeper | Two approval configs, ~30 lines of JSON |
| Audit cost | $10,000+ | Protocol-level enforcement, no custom code to audit |
| Cancellation | Custom function in contract | `MsgPurgeApprovals` -- built into the chain |
| Schedule enforcement | External keeper + gas sponsorship | Chain tracks intervals and resets trackers automatically |
| Composability | Requires contract integration | Works with any approval criteria (ownership gates, merkle proofs, etc.) |

## Related Pages

- [Predetermined Balances](../../token-standard/learn/approval-criteria/predetermined-balances.md) -- for controlling exact transfer amounts and order
- [Approval Trackers](../../token-standard/learn/approval-criteria/approval-trackers.md) -- how the chain tracks cumulative amounts per interval
- [Auto-Deletion Options](../../token-standard/learn/approval-criteria/auto-deletion-options.md) -- purge mechanics and auto-expiry
- [Tallied Approval Amounts](../../token-standard/learn/approval-criteria/tallied-approval-amounts.md) -- per-address and per-interval amount caps

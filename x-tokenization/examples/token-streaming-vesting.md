# Token Vesting and Streaming Schedules: No Smart Contract Required

This guide shows how to build token vesting and streaming schedules on BitBadges using the `incrementedBalances` approval field. No custom smart contract, no middleware, no cron jobs -- the chain handles unlock timing and claim tracking natively.

## The Problem

Token streaming protocols let you distribute tokens linearly over time. An employee gets 1000 tokens vested over 12 months -- roughly 83 tokens unlock each month.

Building this on other chains requires custom Solidity, an audit, and a frontend to manage streams. BitBadges handles it with a single approval configuration.

## How It Works

A vesting schedule on BitBadges uses `incrementedBalances` inside an approval. You define a starting balance and an increment rule. Each time the recipient claims, the chain calculates what has unlocked based on the current time and how many claims have already been made.

The chain tracks claim counts per address automatically.

## Basic Vesting Schedule

This example vests 83 tokens per month for 12 months to a specific recipient.

```json
{
  "approvalId": "employee-vesting",
  "fromListId": "Mint",
  "toListId": "recipientAddress",
  "initiatedByListId": "recipientAddress",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "predeterminedBalances": {
      "manualBalances": [],
      "incrementedBalances": {
        "startBalances": [{
          "amount": "83",
          "badgeIds": [{ "start": "1", "end": "1" }],
          "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }]
        }],
        "incrementBadgeIdsBy": "0",
        "incrementOwnershipTimesBy": "0"
      },
      "orderCalculationMethod": {
        "usePerToAddressNumTransfers": true,
        "useMerkleChallengeLeafIndex": false,
        "useOverallNumTransfers": false,
        "usePerFromAddressNumTransfers": false,
        "usePerInitiatedByAddressNumTransfers": false
      }
    },
    "maxNumTransfers": {
      "perToAddressMaxNumTransfers": "12"
    }
  }
}
```

Key fields:

- **`incrementedBalances.startBalances`** -- the amount unlocked per claim period (83 tokens).
- **`orderCalculationMethod.usePerToAddressNumTransfers`** -- the chain tracks how many times this recipient has claimed and uses that count to calculate which increment applies.
- **`maxNumTransfers.perToAddressMaxNumTransfers`** -- caps the total number of claims at 12 (one per month for 12 months).

## Adding a Time Gate

To ensure tokens only unlock on a monthly schedule (not all at once), add a `resetTimeIntervals` check:

```json
"resetTimeIntervals": {
  "startTime": "1700000000000",
  "intervalLength": "2592000000"
},
"maxNumTransfers": {
  "perToAddressMaxNumTransfers": "1"
}
```

This limits the recipient to one claim per 30-day interval. Combined with 12 total claims, this creates a true monthly vesting schedule.

## Clawback

To enable clawback (revoking unvested tokens), lock the permission so only the collection manager can modify approvals:

1. The manager removes or disables the vesting approval.
2. Remaining tokens stay locked -- the recipient can no longer claim.
3. No custom recovery function needed. The manager simply updates the approval configuration.

```typescript
// Manager revokes vesting by removing the approval
const updateMsg = {
  collectionId: "42",
  collectionApprovals: [
    // Include all other approvals, but omit "employee-vesting"
  ]
};
```

## Multi-Recipient Vesting

For vesting to multiple recipients with different schedules, create separate approvals per recipient. Each approval tracks claims independently because `usePerToAddressNumTransfers` scopes the counter to each address.

Alternatively, use `useOverallNumTransfers` with a shared pool if you want a first-come-first-served model where any recipient can claim from the same stream.

## Conditional Vesting

Add conditions that must be met before a recipient can claim their vested tokens:

### Require Badge Ownership

```json
"mustOwnBadges": [{
  "collectionId": "10",
  "amountRange": { "start": "1", "end": "1" },
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "badgeIds": [{ "start": "1", "end": "1" }]
}]
```

The recipient must hold a specific badge (such as an employee status badge) to claim vested tokens.

### Require Merkle Proof

Add a `merkleChallenge` to the approval criteria so the recipient must provide a merkle proof to claim. Useful for staged airdrops where the recipient list is committed on-chain as a root hash.

## Comparison

| Aspect | EVM (Custom Contract) | BitBadges (Native Approval) |
|--------|----------------------|----------------------------|
| Code required | Custom Solidity + audit | One approval config, ~25 lines of JSON |
| Clawback | Custom recovery function | Manager removes the approval |
| Per-recipient schedules | Separate contract deployments or complex mappings | Separate approvals, same collection |
| Conditional vesting | Additional contract logic | Compose with `mustOwnBadges`, merkle challenges, etc. |
| Time enforcement | Block timestamps + custom logic | Chain-level `resetTimeIntervals` with automatic tracker resets |

## Related Pages

- [Predetermined Balances](../../token-standard/learn/approval-criteria/predetermined-balances.md) -- full reference for `manualBalances` and `incrementedBalances`
- [Approval Trackers](../../token-standard/learn/approval-criteria/approval-trackers.md) -- how the chain tracks transfer counts per address
- [Max Number of Transfers](../../token-standard/learn/approval-criteria/max-number-of-transfers.md) -- capping claims per address or overall
- [Building Collection Approvals](building-collection-approvals.md) -- general guide to approval configuration

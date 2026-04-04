# Minting

> Mint approval patterns including public mint, whitelist mint, creator-only mint, payment-gated mint, and escrow payouts

**Category:** Approval Patterns

## Summary

Required fields for all minting approvals:
- fromListId: "Mint"
- overridesFromOutgoingApprovals: true (REQUIRED for ALL Mint approvals)
- autoApproveAllIncomingTransfers: true in defaultBalances (for public-mint collections)
- predeterminedBalances vs approvalAmounts: incompatible — use one or the other
- orderCalculationMethod: MUST have exactly ONE method set to true (default: useOverallNumTransfers)
- coinTransfers override flags: false for standard payments, true for escrow payouts
- Mint escrow: overrideFromWithApproverAddress: true + overrideToWithInitiator: true (pays the minter from the escrow address)
- amountTrackerId: required when using maxNumTransfers or approvalAmounts

## Instructions

## Minting Configuration

When configuring minting approvals, you create collection approvals with fromListId: "Mint" that allow tokens to be minted from the Mint address.

### Core Structure

All minting approvals MUST have:
- **fromListId**: "Mint" (required for all minting operations)
- **overridesFromOutgoingApprovals**: true (REQUIRED for all Mint approvals)
- **toListId**: Typically "All" or specific address list
- **initiatedByListId**: Who can initiate the mint (typically "All" for public mints)

### 1. Payments Per Mint

Use `coinTransfers` in approvalCriteria to require payment:

```json
{
  "approvalCriteria": {
    "coinTransfers": [{
      "to": "bb1creator...",
      "coins": [{ "denom": "ubadge", "amount": "5000000000" }],
      "overrideFromWithApproverAddress": false,
      "overrideToWithInitiator": false
    }]
  }
}
```

**Important:**
- `overrideFromWithApproverAddress`: false (standard for mint payments)
- `overrideToWithInitiator`: false (standard for mint payments)
- Payment recipient (`to`) should be the creator or approver address

### 2. Incremented Token IDs

Use `predeterminedBalances.incrementedBalances` to automatically increment token IDs:

```json
{
  "approvalCriteria": {
    "predeterminedBalances": {
      "incrementedBalances": {
        "startBalances": [{
          "amount": "1",
          "tokenIds": [{ "start": "1", "end": "1" }],
          "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }]
        }],
        "incrementTokenIdsBy": "1",
        "incrementOwnershipTimesBy": "0",
        "durationFromTimestamp": "0",
        "allowOverrideTimestamp": false,
        "recurringOwnershipTimes": { "startTime": "0", "intervalLength": "0", "chargePeriodLength": "0" },
        "allowOverrideWithAnyValidToken": false
      },
      "orderCalculationMethod": {
        "useOverallNumTransfers": true,
        "usePerToAddressNumTransfers": false,
        "usePerFromAddressNumTransfers": false,
        "usePerInitiatedByAddressNumTransfers": false,
        "useMerkleChallengeLeafIndex": false,
        "challengeTrackerId": ""
      },
      "manualBalances": []
    }
  }
}
```

**CRITICAL: orderCalculationMethod Rule**
- When using `predeterminedBalances`, the `orderCalculationMethod` MUST have exactly ONE method set to `true`
- Default: `useOverallNumTransfers: true` (sequential across all mints)
- Cannot have zero methods true, cannot have multiple methods true

### 3. Auto-Deletions

Use `autoDeletionOptions` to automatically delete approvals after use:

```json
{
  "approvalCriteria": {
    "autoDeletionOptions": {
      "afterOneUse": true,
      "afterOverallMaxNumTransfers": false,
      "allowCounterpartyPurge": false,
      "allowPurgeIfExpired": false
    }
  }
}
```

### 4. Transfer Limits (Max Num Transfers)

Use `maxNumTransfers` to limit how many times minting can occur:

```json
{
  "approvalCriteria": {
    "maxNumTransfers": {
      "overallMaxNumTransfers": "100",
      "perInitiatedByAddressMaxNumTransfers": "1",
      "perToAddressMaxNumTransfers": "0",
      "perFromAddressMaxNumTransfers": "0",
      "amountTrackerId": "mint-tracker-id",
      "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" }
    }
  }
}
```

### 5. Appending Minting Approvals After Creation

To allow minting approvals to be added after collection creation:
- Collection must have appropriate permissions (canUpdateCollectionApprovals not frozen for Mint)
- Approval can be added via separate MsgUniversalUpdateCollection transaction

### Mint Escrow (Free Mints with Payout)

The **Mint Escrow Address** is a special reserved address generated from the collection ID that holds Cosmos native funds. Use `mintEscrowCoinsToTransfer` to fund it during collection creation:

```json
{
  "collectionId": "0",
  "mintEscrowCoinsToTransfer": [{ "denom": "ubadge", "amount": "10000000000" }],
  "collectionApprovals": [{
    "fromListId": "Mint",
    "toListId": "All",
    "initiatedByListId": "All",
    "approvalId": "free-mint",
    "approvalCriteria": {
      "coinTransfers": [{
        "to": "bb1user...",
        "coins": [{ "denom": "ubadge", "amount": "1000000000" }],
        "overrideFromWithApproverAddress": true,
        "overrideToWithInitiator": true
      }],
      "overridesFromOutgoingApprovals": true
    }
  }]
}
```

Key escrow rules:
- **overrideFromWithApproverAddress: true** — uses mint escrow as the payer
- **overrideToWithInitiator: true** — pays the user who initiated the mint
- Escrow address has no private key, only collection approvals can transfer from it

### Complete Example: Public Mint with Payment and Incremented IDs

```json
{
  "collectionApprovals": [{
    "fromListId": "Mint",
    "toListId": "All",
    "initiatedByListId": "All",
    "approvalId": "public-mint-5-badge",
    "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
    "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "approvalCriteria": {
      "overridesFromOutgoingApprovals": true,
      "coinTransfers": [{
        "to": "bb1creator...",
        "coins": [{ "denom": "ubadge", "amount": "5000000000" }],
        "overrideFromWithApproverAddress": false,
        "overrideToWithInitiator": false
      }],
      "predeterminedBalances": {
        "incrementedBalances": {
          "startBalances": [{ "amount": "1", "tokenIds": [{ "start": "1", "end": "1" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }],
          "incrementTokenIdsBy": "1",
          "incrementOwnershipTimesBy": "0",
          "durationFromTimestamp": "0",
          "allowOverrideTimestamp": false,
          "recurringOwnershipTimes": { "startTime": "0", "intervalLength": "0", "chargePeriodLength": "0" },
          "allowOverrideWithAnyValidToken": false
        },
        "orderCalculationMethod": {
          "useOverallNumTransfers": true,
          "usePerToAddressNumTransfers": false,
          "usePerFromAddressNumTransfers": false,
          "usePerInitiatedByAddressNumTransfers": false,
          "useMerkleChallengeLeafIndex": false,
          "challengeTrackerId": ""
        },
        "manualBalances": []
      },
      "maxNumTransfers": {
        "overallMaxNumTransfers": "1000",
        "perInitiatedByAddressMaxNumTransfers": "1",
        "perToAddressMaxNumTransfers": "0",
        "perFromAddressMaxNumTransfers": "0",
        "amountTrackerId": "public-mint-tracker",
        "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" }
      }
    }
  }]
}
```

### Minting Gotchas

- **MUST have overridesFromOutgoingApprovals: true** (required for all Mint approvals)
- **coinTransfers override flags**: Should be false for standard payments, true for escrow payouts
- **predeterminedBalances vs approvalAmounts**: These are incompatible — use one or the other
- **orderCalculationMethod**: MUST have exactly ONE method set to true
- **amountTrackerId**: Required when using maxNumTransfers or approvalAmounts
- **autoApproveAllIncomingTransfers**: Must be true in defaultBalances for public-mint collections

## Common Mistakes

- DON'T use numbers instead of strings for amounts — use "1000" not 1000. All numeric values in BitBadges JSON must be string-encoded.
- DON'T forget overridesFromOutgoingApprovals: true on Mint approvals — without it, the Mint address cannot send tokens and minting silently fails.
- DON'T forget autoApproveAllIncomingTransfers: true in defaultBalances for public-mint collections — otherwise recipients cannot receive minted tokens.
- DON'T forget to add prioritizedApprovals in MsgTransferTokens — even if empty ([]), this field must be present or the transfer fails.
- DON'T use predeterminedBalances and approvalAmounts together — they are incompatible. Use one or the other.
- DON'T set multiple methods to true in orderCalculationMethod — exactly ONE must be true (default: useOverallNumTransfers).

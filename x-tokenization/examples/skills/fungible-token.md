# Fungible Token

> Simple fungible token with fixed or unlimited supply and configurable mint/transfer approvals

**Category:** Token Types

## Summary

Required standards: ["Fungible Tokens"]

- validTokenIds: MUST be exactly [{ "start": "1", "end": "1" }] (single token ID)
- All tokens share the same token ID (1), making them interchangeable
- Amount field in transfers determines quantity
- Token metadata must reference token ID 1
- Ownership times typically forever: [{ "start": "1", "end": "18446744073709551615" }]

## Instructions

## Fungible Token Configuration

When creating a fungible token collection, you MUST follow these requirements:

### Required Configuration

1. **validTokenIds**: Set to exactly [{ "start": "1", "end": "1" }]
   - This ensures only token ID 1 is valid
   - This is the standard pattern for fungible tokens

2. **Standards**: Include "Fungible Tokens" in the standards array
   - Example: "standards": ["Fungible Tokens"]

3. **Token Metadata**: Each tokenMetadata entry must reference token ID 1
   - Example: "tokenIds": [{ "start": "1", "end": "1" }]

### Supply Tracking with approvalAmounts

Fungible tokens use approvalAmounts (NOT predeterminedBalances) to track minting supply:

```json
{
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "approvalAmounts": {
      "overallApprovalAmount": "1000000",
      "perInitiatedByAddressApprovalAmount": "1000",
      "perToAddressApprovalAmount": "0",
      "perFromAddressApprovalAmount": "0",
      "amountTrackerId": "mint-tracker",
      "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" }
    }
  }
}
```

- overallApprovalAmount: total supply cap (use "0" for unlimited)
- perInitiatedByAddressApprovalAmount: max per user (use "0" for unlimited)
- IMPORTANT: predeterminedBalances and approvalAmounts are INCOMPATIBLE — fungible tokens use approvalAmounts, NOT predeterminedBalances

### Pattern-Specific Gotchas

- All tokens share the same token ID (1), making them interchangeable
- Amount field in transfers determines quantity (e.g., "amount": "100" means 100 tokens)
- Ownership times are typically forever: [{ "start": "1", "end": "18446744073709551615" }]

## Common Mistakes

- DON'T use predeterminedBalances for fungible tokens — use approvalAmounts instead. They are incompatible.
- DON'T use numbers instead of strings for amounts — use "1000" not 1000. All numeric values must be string-encoded.
- DON'T forget autoApproveAllIncomingTransfers: true in defaultBalances — without it, recipients cannot receive tokens on public-mint collections.
- DON'T forget overridesFromOutgoingApprovals: true on Mint approvals — required for all fromListId: "Mint" approvals.
- DON'T use multiple token IDs — fungible tokens must use exactly one token ID [{ "start": "1", "end": "1" }].

### Example Structure

```json
{
  "updateValidTokenIds": true,
  "validTokenIds": [{ "start": "1", "end": "1" }],
  "updateStandards": true,
  "standards": ["Fungible Tokens"],
  "updateTokenMetadata": true,
  "tokenMetadata": [{
    "uri": "ipfs://...",
    "customData": "",
    "tokenIds": [{ "start": "1", "end": "1" }]
  }]
}
```

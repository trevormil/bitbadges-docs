# Products

> Multi-product storefront with per-product pricing, supply limits, and optional burn-on-purchase. Each product is a separate token ID.

**Category:** Token Types

## Summary

Required standards: ["Products"]

- N token IDs (one per product), starting at 1
- N+1 approvals: 1 purchase approval per product + 1 optional burn approval
- Each purchase approval: fromListId="Mint", toListId="All" (or burn address if burn-on-purchase), 1 coinTransfer paying the store address
- Payment goes directly to store address (NOT to escrow) — overrideFromWithApproverAddress: false
- Each product has independent price, supply limit (maxNumTransfers), and burn-on-purchase toggle
- predeterminedBalances.startBalances: 1x of that product's token ID
- Optional burn approval: !Mint → burn address, no coinTransfers
- invariants: { noCustomOwnershipTimes: true }
- All permissions frozen after creation
- DON'T use overrideFromWithApproverAddress — payment goes directly to store, not from escrow
- DON'T use allowAmountScaling — fixed price per item
- DON'T use votingChallenges, merkleChallenges, or mustOwnTokens
- DO use unique approvalId per product (e.g. "product-purchase-1", "product-purchase-2")
- DO set maxNumTransfers to supply limit (0 = unlimited)

## Instructions

## Products Configuration

### Mental Model

A multi-product storefront where each product is a separate token ID. Buyers pay coins to mint a product token. Each product has its own price, supply limit, and optional burn-on-purchase setting. Payment goes directly to the store owner's address (not escrow).

### Collection Structure

- Token IDs 1..N (one per product)
- Standard: "Products"
- validTokenIds: [{ start: "1", end: "<NUM_PRODUCTS>" }]
- invariants: { noCustomOwnershipTimes: true }
- All permissions frozen after creation

### Approval Structure

Each product gets its own purchase approval. There's also an optional global burn approval.

#### Purchase Approval (per product)

```json
{
  "approvalId": "product-purchase-1",
  "fromListId": "Mint",
  "toListId": "All",
  "initiatedByListId": "All",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": true,
    "coinTransfers": [{
      "to": "<STORE_ADDRESS>",
      "overrideFromWithApproverAddress": false,
      "overrideToWithInitiator": false,
      "coins": [{ "amount": "<PRICE>", "denom": "<DENOM>" }]
    }],
    "predeterminedBalances": {
      "incrementedBalances": {
        "startBalances": [{ "amount": "1", "tokenIds": [{ "start": "1", "end": "1" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }],
        "incrementTokenIdsBy": "0",
        "incrementOwnershipTimesBy": "0",
        "durationFromTimestamp": "0",
        "allowOverrideTimestamp": false,
        "allowAmountScaling": false,
        "maxScalingMultiplier": "0"
      },
      "orderCalculationMethod": { "useOverallNumTransfers": true }
    },
    "maxNumTransfers": { "overallMaxNumTransfers": "<SUPPLY_LIMIT_OR_0>" }
  }
}
```

> For burn-on-purchase products, set toListId to the burn address instead of "All". The buyer never holds the token — it's minted directly to burn, and the purchase receipt is the transaction itself.

#### Burn Approval (optional, for all products)

```json
{
  "approvalId": "product-burn",
  "fromListId": "!Mint",
  "toListId": "<BURN_ADDRESS>",
  "initiatedByListId": "All",
  "tokenIds": [{ "start": "1", "end": "<NUM_PRODUCTS>" }],
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": true,
    "coinTransfers": [],
    "maxNumTransfers": { "overallMaxNumTransfers": "0" }
  }
}
```

### Creation Flow (Tool Calls)

1. \`set_valid_token_ids\` — set [{ start: "1", end: "<NUM_PRODUCTS>" }]
2. \`set_standards\` — set ["Products"]
3. \`set_invariants\` — set { noCustomOwnershipTimes: true }
4. \`add_approval\` xN — one purchase approval per product
5. \`add_approval\` — optional burn approval
6. \`set_collection_metadata\` — store name, description, image
7. \`set_token_metadata\` xN — metadata for each product
8. \`set_permissions\` — preset "fully-immutable"
9. \`validate_transaction\` — verify structure
10. \`simulate_transaction\` — dry run

### Common Mistakes

- DON'T use overrideFromWithApproverAddress on purchase approvals — payment goes directly to the store address, not from escrow
- DON'T use allowAmountScaling — each purchase is exactly 1 item at fixed price
- DON'T use a single approval for multiple products — each product needs its own approval with its own tokenIds, price, and supply limit
- DON'T forget unique approvalIds — duplicate IDs will cause the chain to reject the transaction
- DON'T set maxNumTransfers > 0 on the burn approval — burns should be unlimited
- DON'T use votingChallenges, merkleChallenges, or mustOwnTokens — purchases are open to all
- DON'T forget to set toListId to burn address for burn-on-purchase products

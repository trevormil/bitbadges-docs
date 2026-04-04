# Auction

> Single-item auction with intent-based bidding. Seller mints NFT directly to the winning bidder during the accept window.

**Category:** Token Types

## Summary

Required standards: ["Auction"]

- 1 token ID (the auctioned item)
- 2 collection-level approvals: mint-to-winner (combines mint + accept), burn (cleanup)
- Mint-to-winner: seller mints NFT directly to winning bidder during accept window (bidDeadline → bidDeadline + acceptWindow)
- No separate mint-at-creation step — token doesn't exist until seller accepts a bid
- Burn: anyone can burn token to burn address (permanent cleanup)
- Bidding via user-level outgoing approval intents (not collection approvals)
- Bids must have transferTimes valid through end of accept window (not just bid deadline)
- initiatedByListId on mint-to-winner = seller address (only seller can accept)
- maxNumTransfers = 1 on all approvals (one-shot)
- overridesToIncomingApprovals: false on mint-to-winner (bidder's incoming approval handles payment)
- All permissions frozen after creation
- DON'T use coinTransfers on collection approvals — payment happens via intent matching
- DON'T set initiatedByListId to "All" on mint-to-winner — must be seller
- DON'T set transferTimes to forever — must be bounded to accept window
- DO use autoDeletionOptions.afterOneUse: true on mint-to-winner

## Instructions

## Auction Configuration

### Mental Model

A single-item auction where the seller creates a collection, bidders place intent-based bids, and the seller mints the NFT directly to the winning bidder during the accept window. There is no separate mint-then-transfer flow — mint + accept happen in one action. The collection has NO coinTransfers — payment is handled entirely through the bidder's user-level approval (intent matching).

### Collection Structure

- Token ID 1 = The auctioned item
- Standard: "Auction"
- validTokenIds: [{ start: "1", end: "1" }]
- invariants: { noCustomOwnershipTimes: true }
- All permissions frozen after creation

### Bidding Mechanism

Bidders set user-level outgoing approvals on their own accounts that say "I will pay X coins for token 1 from this collection." The seller then accepts the best bid by minting the token directly to the winning bidder during the accept window. The bidder's outgoing approval handles the coin payment side via intent matching.

Bids must have transferTimes that stay valid through the END of the accept window (not just the bid deadline), so the seller can match them during the entire accept period.

### 2 Approvals

#### 1. Mint-to-Winner (seller mints NFT directly to winning bidder during accept window)

\`\`\`json
{
  "approvalId": "auction-mint-to-winner",
  "fromListId": "Mint",
  "toListId": "All",
  "initiatedByListId": "<SELLER_ADDRESS>",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "transferTimes": [{ "start": "<BID_DEADLINE>", "end": "<BID_DEADLINE + ACCEPT_WINDOW>" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": false,
    "coinTransfers": [],
    "predeterminedBalances": {
      "incrementedBalances": {
        "startBalances": [{ "amount": "1", "tokenIds": [{ "start": "1", "end": "1" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }]
      },
      "orderCalculationMethod": { "useOverallNumTransfers": true }
    },
    "maxNumTransfers": { "overallMaxNumTransfers": "1" },
    "autoDeletionOptions": { "afterOneUse": true, "afterOverallMaxNumTransfers": true }
  }
}
\`\`\`

> **CRITICAL:** \`overridesToIncomingApprovals: false\` — the bidder's incoming approval (intent) must be checked so the coin payment side executes. \`transferTimes\` are bounded to the accept window only — the seller cannot mint before the bid deadline. \`toListId: "All"\` allows minting to any address (the winning bidder).

#### 2. Burn (cleanup — anyone can burn the token)

\`\`\`json
{
  "approvalId": "auction-burn",
  "fromListId": "!Mint",
  "toListId": "<BURN_ADDRESS>",
  "initiatedByListId": "All",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": true,
    "coinTransfers": [],
    "maxNumTransfers": { "overallMaxNumTransfers": "1" },
    "autoDeletionOptions": { "afterOneUse": true, "afterOverallMaxNumTransfers": true }
  }
}
\`\`\`

### Auction Flow

1. **Create**: Seller creates auction collection with 2 approvals. No token is minted yet.
2. **Bid**: Bidders place intent-based bids (user-level outgoing approvals with coin payment offers). Bids must have transferTimes valid through end of accept window.
3. **Accept**: After bid deadline, seller mints token 1 directly to the winning bidder's address. The bidder's outgoing approval triggers coin payment via intent matching. One action: mint + payment.
4. **Cleanup**: If unsold, burn approval allows cleanup.

### Creation Flow (Tool Calls)

1. \`build_token\` — initialize collection
2. \`set_valid_token_ids\` — set [{ start: "1", end: "1" }]
3. \`set_standards\` — set ["Auction"]
4. \`set_invariants\` — set { noCustomOwnershipTimes: true }
5. \`add_approval\` x2 — mint-to-winner, burn
6. \`set_collection_metadata\` — auction title, description, image
7. \`set_token_metadata\` — token 1 metadata (the item being auctioned)
8. \`set_permissions\` — preset "fully-immutable"
9. \`validate_transaction\` — verify structure
10. \`simulate_transaction\` — dry run

### Common Mistakes

- DON'T add coinTransfers to collection approvals — payment flows through intent matching, not collection-level coin transfers
- DON'T set initiatedByListId to "All" on mint-to-winner — only the seller can accept bids
- DON'T set transferTimes to forever — MUST be bounded to accept window (bidDeadline → bidDeadline + acceptWindow)
- DON'T set overridesToIncomingApprovals to true on mint-to-winner — must be false so the bidder's incoming approval (payment intent) is checked
- DON'T create a separate mint-to-seller approval — the token should not exist until the seller accepts a bid
- DON'T forget autoDeletionOptions on mint-to-winner — without afterOneUse: true, the seller could mint to multiple bidders
- DON'T forget that bids must have transferTimes valid through the END of the accept window, not just the bid deadline

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

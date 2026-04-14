# Credit Token

> Increment-only, non-transferable credit token purchased with any ICS20 denom. Users pay X of a denom and receive Y tokens as credits/proof of payment. For a 1:1 backed token with on-chain transferability, use the Smart Token standard instead.

**Category:** Token Types

## Summary

Required standards: ["Credit Token"]

- Increment-only, non-transferable (soulbound) fungible token purchased with ICS20 denom
- validTokenIds: [{ "start": "1", "end": "1" }] (single token ID)
- collectionApprovals: ONLY Mint approvals (no transferable/burnable approvals)
- Lock canUpdateCollectionApprovals (empty array = frozen)
- defaultBalances: autoApproveAllIncomingTransfers: true, autoApproveSelfInitiatedOutgoingTransfers: true, autoApproveSelfInitiatedIncomingTransfers: true
- All Mint approvals: overridesFromOutgoingApprovals: true, mustPrioritize: true
- Denomination tiers: create 8-10 approval tiers (credit-1, credit-5, credit-10, etc.) for greedy decomposition
- MUST include alias path for display
- All permissions locked (empty arrays)
- Key difference from Smart Token: one-way minting only, no backing/unbacking, no transferability

## Instructions

## Credit Token Configuration

### Concept

A Credit Token is an increment-only, non-transferable fungible token that users purchase with an ICS20 denom (USDC, ATOM, BADGE, etc.). Tokens serve as both proof of payment and consumable credits. This is a one-way system — tokens can only be minted (incremented), never sold back, burned, or transferred between users. For a 1:1 backed token with on-chain transferability, use the Smart Token standard instead.

### Payment Flow

When a user mints credit tokens, the payment (coinTransfers) goes directly to a **payout address** specified in the approval. The tokens are NOT redeemable post-mint — they are increment-only. The payout address receives the ICS20 denom immediately upon mint; there is no escrow or redemption mechanism.

### Usage Pattern: totalUsed / totalCreditsPaidFor

Credit tokens are designed for systems that track consumption off-chain. The on-chain token balance represents `totalCreditsPaidFor` — the total credits ever purchased. An off-chain system tracks `totalUsed`. The remaining budget is simply `balance - totalUsed`.

**Example: BitBadges AI Builder (Collection 23, AITOKEN)**
- User purchases 10 USDC → receives 1,000,000 AITOKEN (on-chain balance = 1,000,000)
- User makes AI builder requests → backend tracks `totalUsed` (e.g., 250,000 tokens used)
- Remaining budget = on-chain balance (1,000,000) - totalUsed (250,000) = 750,000
- User purchases 5 more USDC → on-chain balance increments to 2,000,000
- Remaining budget = 2,000,000 - 250,000 = 1,750,000
- The balance only ever goes up (increment-only). The off-chain `totalUsed` only ever goes up. Budget = balance - totalUsed.

### Required Structure

1. **Standards**: MUST include "Credit Token"
   - `"standards": ["Credit Token"]`

2. **validTokenIds**: Single fungible token ID
   - `"validTokenIds": [{ "start": "1", "end": "1" }]`

3. **Non-transferable (Soulbound)**: Only mint approvals allowed
   - `collectionApprovals` should ONLY contain approvals with `"fromListId": "Mint"`
   - No transferable-approval or burnable-approval
   - Lock `canUpdateCollectionApprovals` (empty array = frozen)

4. **Auto-approve incoming**: defaultBalances MUST have:
   - `"autoApproveAllIncomingTransfers": true`
   - `"autoApproveSelfInitiatedOutgoingTransfers": true`
   - `"autoApproveSelfInitiatedIncomingTransfers": true`

5. **Mint Approvals with coinTransfers**: Each approval mints tokens in exchange for payment
   - `"fromListId": "Mint"`
   - `"toListId": "All"`
   - `"initiatedByListId": "All"`
   - `"overridesFromOutgoingApprovals": true` (REQUIRED for all Mint approvals)
   - `"mustPrioritize": true`
   - `coinTransfers`: specifies payment amount and recipient

### Conversion Rate Pattern

The conversion rate is: X base units of ICS20 denom = Y tokens minted.

For example, if 1 USDC (1,000,000 base units) = 100,000 tokens:
- `coinTransfers.coins[0].amount`: "1000000" (1 USDC in base units)
- `predeterminedBalances.incrementedBalances.startBalances[0].amount`: "100000" (tokens minted)

### Denomination Tiers

Create multiple approval tiers for bulk purchases. Each tier has a unique `approvalId` (`credit-<multiplier>`) and mints a proportional amount of tokens. The frontend uses greedy decomposition to break any purchase amount into the fewest transactions.

**Choose 8-10 tiers** that make sense for the token's use case and expected purchase sizes. Cover a wide range from small to very large. The tiers are NOT hardcoded — pick the most applicable and efficient denominations for the specific token.

Example tiers (1 USDC = 100K tokens, but adapt to your use case):
| approvalId | Payment | Tokens Minted |
|---|---|---|
| credit-1 | 1 USDC | 100,000 |
| credit-5 | 5 USDC | 500,000 |
| credit-10 | 10 USDC | 1,000,000 |
| credit-50 | 50 USDC | 5,000,000 |
| credit-100 | 100 USDC | 10,000,000 |
| credit-500 | 500 USDC | 50,000,000 |
| credit-1000 | 1,000 USDC | 100,000,000 |
| credit-10000 | 10,000 USDC | 1,000,000,000 |
| credit-100000 | 100,000 USDC | 10,000,000,000 |
| credit-1000000000 | 1B USDC | 100T tokens |

The multiplier in the approvalId (e.g., `credit-50`) represents the number of base payment units. Each tier's payment = multiplier × base payment amount, and tokens minted = multiplier × tokens per unit.

### Alias Path (REQUIRED)

MUST include an alias path so tokens display nicely:
```json
"aliasPathsToAdd": [{
  "denom": "u<symbol_lowercase>",
  "conversion": {
    "sideA": { "amount": "1" },
    "sideB": [{ "amount": "1", "ownershipTimes": [{"start":"1","end":"18446744073709551615"}], "tokenIds": [{"start":"1","end":"1"}] }]
  },
  "symbol": "<SYMBOL>",
  "denomUnits": [],
  "metadata": { "uri": "ipfs://METADATA_ALIAS_u<symbol_lowercase>", "customData": "" }
}]
```

### Approval Template

Each mint approval follows this structure:
```json
{
  "toListId": "All",
  "fromListId": "Mint",
  "initiatedByListId": "All",
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "tokenIds": [{ "start": "1", "end": "1" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalId": "credit-<amount>",
  "uri": "",
  "customData": "",
  "approvalCriteria": {
    "predeterminedBalances": {
      "manualBalances": [],
      "incrementedBalances": {
        "startBalances": [{ "amount": "<tokens_to_mint>", "tokenIds": [{"start":"1","end":"1"}], "ownershipTimes": [{"start":"1","end":"18446744073709551615"}] }],
        "incrementTokenIdsBy": "0",
        "incrementOwnershipTimesBy": "0",
        "allowOverrideTimestamp": false,
        "recurringOwnershipTimes": { "startTime": "0", "intervalLength": "0", "chargePeriodLength": "0" },
        "allowOverrideWithAnyValidToken": false
      },
      "orderCalculationMethod": { "useOverallNumTransfers": true, "usePerToAddressNumTransfers": false, "usePerFromAddressNumTransfers": false, "usePerInitiatedByAddressNumTransfers": false, "useMerkleChallengeLeafIndex": false, "challengeTrackerId": "" }
    },
    "approvalAmounts": { "overallApprovalAmount": "0", "perToAddressApprovalAmount": "0", "perFromAddressApprovalAmount": "0", "perInitiatedByAddressApprovalAmount": "0", "amountTrackerId": "credit-<amount>", "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" } },
    "maxNumTransfers": { "overallMaxNumTransfers": "0", "perToAddressMaxNumTransfers": "0", "perFromAddressMaxNumTransfers": "0", "perInitiatedByAddressMaxNumTransfers": "0", "amountTrackerId": "credit-<amount>", "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" } },
    "coinTransfers": [{
      "to": "<payment_recipient_address>",
      "coins": [{ "amount": "<payment_base_units>", "denom": "<ics20_denom>" }],
      "overrideFromWithApproverAddress": false,
      "overrideToWithInitiator": false
    }],
    "merkleChallenges": [],
    "mustOwnTokens": [],
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": false,
    "mustPrioritize": true
  },
  "version": 0
}
```

### Permissions (All Locked)

All permissions should be locked (empty arrays = frozen):
```json
"collectionPermissions": {
  "canDeleteCollection": [],
  "canArchiveCollection": [],
  "canUpdateStandards": [],
  "canUpdateCustomData": [],
  "canUpdateManager": [],
  "canUpdateCollectionMetadata": [],
  "canUpdateValidTokenIds": [],
  "canUpdateTokenMetadata": [],
  "canUpdateCollectionApprovals": [],
  "canAddMoreAliasPaths": [],
  "canAddMoreCosmosCoinWrapperPaths": []
}
```

### Key Differences from Smart Token

- **Increment-only** — tokens can only be minted (purchased), never redeemed, burned, or decreased
- **Non-transferable** — soulbound, no peer-to-peer transfers. If users need transferability, use Smart Token instead
- **No backing/unbacking** — one-way minting only, no cosmosCoinBackedPath
- **Multiple tiers** — different approval amounts for bulk purchases via greedy decomposition
- **Credits never expire** — ownership times cover full range

### Frontend Integration

The Credit Token standard has a dedicated view page that shows:
1. User's current token balance (using the alias path for display)
2. Purchase form with DenomAmountSelectWithMax for the payment denom
3. Conversion rate display (X denom = Y tokens)
4. Multi-tier transaction decomposition for optimal gas usage

## Common Mistakes

- DON'T add transferable or burnable approvals — credit tokens are increment-only and non-transferable (soulbound). Only Mint approvals are allowed.
- DON'T forget mustPrioritize: true on all credit token Mint approvals — required for correct tier matching.
- DON'T forget the alias path — credit tokens will not display properly without one.
- DON'T use numbers instead of strings for amounts — all values must be string-encoded ("100000" not 100000).
- DON'T confuse credit tokens with smart tokens — credit tokens are one-way minting only with no backing/unbacking or transferability.

## Reference Collections

- [Collection 23](https://bitbadges.io/collections/23)

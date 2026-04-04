# Crowdfund

> On-chain crowdfunding with goal tracking via mustOwnTokens. Contributors deposit funds, receive refund tokens. Crowdfunder withdraws if goal met, contributors refund if not.

**Category:** Token Types

## Summary

Required standards: ["Crowdfund"]

- 2 token IDs: Token 1 = Refund token (contributor holds), Token 2 = Progress token (crowdfunder accumulates)
- 4 collection-level approvals: deposit-refund, deposit-progress, success (withdraw), refund
- Contributors deposit coins → receive Token 1 (refund token). Paired approval mints Token 2 to crowdfunder (progress tracking).
- Success: crowdfunder withdraws if mustOwnTokens confirms they hold >= goal of Token 2 (collectionId: 0 = self-reference)
- Refund: after deadline, contributors burn Token 1 → escrow pays them back (only if goal NOT met via mustOwnTokens check)
- allowAmountScaling: true on ALL 4 approvals (contributors choose deposit size, everything scales proportionally)
- maxScalingMultiplier: MAX_UINT for unrestricted scaling
- Deposit coinTransfer.to = "Mint" (auto-resolves to escrow)
- requireToEqualsInitiatedBy: true on deposit-refund (contributor receives their own refund token)
- All permissions frozen after creation
- DON'T use votingChallenges — goal tracking is via mustOwnTokens, not voting
- DON'T forget allowAmountScaling on ALL 4 approvals
- DON'T set overrideFromWithApproverAddress on deposit (contributor pays, not escrow)
- DO set overrideFromWithApproverAddress: true on success and refund (escrow pays out)
- DO set overrideToWithInitiator: true on refund (contributor receives their own refund)
- DO use collectionId: "0" in mustOwnTokens for self-reference

## Instructions

## Crowdfund Configuration

### Mental Model

On-chain crowdfunding with automatic goal tracking. Contributors deposit coins and receive refund tokens. A progress token tracks total raised. If the goal is met, the crowdfunder withdraws all funds. If not, contributors burn their refund tokens to get deposits back.

### Collection Structure

- Token ID 1 = Refund token (contributor holds — burn to refund)
- Token ID 2 = Progress token (crowdfunder accumulates — tracks total raised)
- Standard: "Crowdfund"
- validTokenIds: [{ start: "1", end: "2" }]
- invariants: { noCustomOwnershipTimes: true }
- All permissions frozen after creation

### 4 Required Approvals

#### 1. Deposit-Refund (contributor pays coins → receives Token 1)

```json
{
  "approvalId": "deposit-refund",
  "fromListId": "Mint",
  "toListId": "All",
  "initiatedByListId": "All",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "transferTimes": [{ "start": "1", "end": "<DEADLINE_TIMESTAMP>" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": true,
    "requireToEqualsInitiatedBy": true,
    "coinTransfers": [{
      "to": "Mint",
      "overrideFromWithApproverAddress": false,
      "overrideToWithInitiator": false,
      "coins": [{ "amount": "1", "denom": "<DEPOSIT_DENOM>" }]
    }],
    "predeterminedBalances": {
      "incrementedBalances": {
        "startBalances": [{ "amount": "1", "tokenIds": [{ "start": "1", "end": "1" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }],
        "allowAmountScaling": true,
        "maxScalingMultiplier": "18446744073709551615",
        "incrementTokenIdsBy": "0",
        "incrementOwnershipTimesBy": "0",
        "durationFromTimestamp": "0",
        "allowOverrideTimestamp": false
      },
      "orderCalculationMethod": { "useOverallNumTransfers": true }
    },
    "maxNumTransfers": { "overallMaxNumTransfers": "0" }
  }
}
```

> **CRITICAL:** `requireToEqualsInitiatedBy: true` ensures the contributor receives their own refund token. `allowAmountScaling: true` lets contributors choose their deposit size — the coin payment and token amount scale together.

#### 2. Deposit-Progress (paired: mints Token 2 to crowdfunder, no coinTransfer)

```json
{
  "approvalId": "deposit-progress",
  "fromListId": "Mint",
  "toListId": "<CROWDFUNDER_ADDRESS>",
  "initiatedByListId": "All",
  "tokenIds": [{ "start": "2", "end": "2" }],
  "transferTimes": [{ "start": "1", "end": "<DEADLINE_TIMESTAMP>" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": true,
    "coinTransfers": [],
    "predeterminedBalances": {
      "incrementedBalances": {
        "startBalances": [{ "amount": "1", "tokenIds": [{ "start": "2", "end": "2" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }],
        "allowAmountScaling": true,
        "maxScalingMultiplier": "18446744073709551615",
        "incrementTokenIdsBy": "0",
        "incrementOwnershipTimesBy": "0",
        "durationFromTimestamp": "0",
        "allowOverrideTimestamp": false
      },
      "orderCalculationMethod": { "useOverallNumTransfers": true }
    },
    "maxNumTransfers": { "overallMaxNumTransfers": "0" }
  }
}
```

> **toListId** is the crowdfunder's specific address (not "All"). No coinTransfer — this is the paired counterpart to deposit-refund.

#### 3. Success / Withdraw (crowdfunder withdraws if goal met)

```json
{
  "approvalId": "success-withdraw",
  "fromListId": "Mint",
  "toListId": "<BURN_ADDRESS>",
  "initiatedByListId": "<CROWDFUNDER_ADDRESS>",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "transferTimes": [{ "start": "<DEADLINE + 1>", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": true,
    "coinTransfers": [{
      "to": "<CROWDFUNDER_ADDRESS>",
      "overrideFromWithApproverAddress": true,
      "overrideToWithInitiator": false,
      "coins": [{ "amount": "1", "denom": "<DEPOSIT_DENOM>" }]
    }],
    "mustOwnTokens": [{
      "collectionId": "0",
      "tokenIds": [{ "start": "2", "end": "2" }],
      "amountRange": { "start": "<GOAL_AMOUNT>", "end": "18446744073709551615" },
      "ownershipCheckParty": "<CROWDFUNDER_ADDRESS>"
    }],
    "predeterminedBalances": {
      "incrementedBalances": {
        "startBalances": [{ "amount": "1", "tokenIds": [{ "start": "1", "end": "1" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }],
        "allowAmountScaling": true,
        "maxScalingMultiplier": "18446744073709551615",
        "incrementTokenIdsBy": "0",
        "incrementOwnershipTimesBy": "0",
        "durationFromTimestamp": "0",
        "allowOverrideTimestamp": false
      },
      "orderCalculationMethod": { "useOverallNumTransfers": true }
    },
    "maxNumTransfers": { "overallMaxNumTransfers": "1" }
  }
}
```

> **mustOwnTokens with collectionId: "0"** = self-reference. Checks that the crowdfunder owns >= goal amount of Token 2 (progress token). Only available after deadline.

#### 4. Refund (contributor burns Token 1 → gets deposit back, only if goal NOT met)

```json
{
  "approvalId": "refund",
  "fromListId": "!Mint",
  "toListId": "<BURN_ADDRESS>",
  "initiatedByListId": "All",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "transferTimes": [{ "start": "<DEADLINE + 1>", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "overridesToIncomingApprovals": true,
    "coinTransfers": [{
      "to": "",
      "overrideFromWithApproverAddress": true,
      "overrideToWithInitiator": true,
      "coins": [{ "amount": "1", "denom": "<DEPOSIT_DENOM>" }]
    }],
    "mustOwnTokens": [{
      "collectionId": "0",
      "tokenIds": [{ "start": "2", "end": "2" }],
      "amountRange": { "start": "0", "end": "<GOAL_AMOUNT - 1>" },
      "ownershipCheckParty": "<CROWDFUNDER_ADDRESS>"
    }],
    "predeterminedBalances": {
      "incrementedBalances": {
        "startBalances": [{ "amount": "1", "tokenIds": [{ "start": "1", "end": "1" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }],
        "allowAmountScaling": true,
        "maxScalingMultiplier": "18446744073709551615",
        "incrementTokenIdsBy": "0",
        "incrementOwnershipTimesBy": "0",
        "durationFromTimestamp": "0",
        "allowOverrideTimestamp": false
      },
      "orderCalculationMethod": { "useOverallNumTransfers": true }
    },
    "maxNumTransfers": {
      "overallMaxNumTransfers": "18446744073709551615"
    }
  }
}
```

> Refund uses overrideFromWithApproverAddress: true (escrow pays) + overrideToWithInitiator: true (contributor receives). mustOwnTokens checks crowdfunder has LESS than goal of Token 2 (amountRange.end = goal - 1). allowAmountScaling: true so refund scales with deposit size. maxNumTransfers = MAX_UINT (needs non-zero with overrideFromWithApproverAddress).

### Creation Flow (Tool Calls)

1. \`build_token\` — initialize collection
2. \`set_valid_token_ids\` — set [{ start: "1", end: "2" }]
3. \`set_standards\` — set ["Crowdfund"]
4. \`set_invariants\` — set { noCustomOwnershipTimes: true }
5. \`add_approval\` x4 — deposit-refund, deposit-progress, success, refund
6. \`set_collection_metadata\` — name, description, image
7. \`set_token_metadata\` x2 — Token 1 (Refund), Token 2 (Progress)
8. \`set_permissions\` — preset "fully-immutable"
9. \`validate_transaction\` — verify structure
10. \`simulate_transaction\` — dry run

### Common Mistakes

- DON'T forget allowAmountScaling: true on ALL 4 approvals — without it, all deposits are fixed at 1 base unit
- DON'T use votingChallenges — goal tracking uses mustOwnTokens, not voting
- DON'T forget maxScalingMultiplier: MAX_UINT — without it, scaling is capped at 0 (no scaling)
- DON'T set overrideFromWithApproverAddress on deposit-refund or deposit-progress (contributor pays, not escrow)
- DON'T forget requireToEqualsInitiatedBy: true on deposit-refund
- DON'T forget the paired deposit-progress approval — it tracks total raised
- DON'T set collectionId to the actual collection ID in mustOwnTokens — use "0" for self-reference
- DON'T forget that success transferTimes must start AFTER the deadline (deadline + 1)
- DON'T forget that refund mustOwnTokens amountRange.end = goal - 1 (strictly less than goal)
- DON'T set maxNumTransfers = 0 on refund approval — overrideFromWithApproverAddress requires non-zero

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

# Burnable

> Allow token holders to burn tokens by sending them to the burn address, permanently removing them from circulation

**Category:** Approval Patterns

## Summary

Allows holders to permanently destroy tokens by sending to burn address.

- Burn address: bb1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqs7gvmv (ETH null address in BitBadges format)
- Approval structure: fromListId: "!Mint", toListId: burn address
- overridesToIncomingApprovals: true (burn address has no user-level incoming approvals)
- approvalId: "burnable-approval" (standard ID used by frontend to detect burnability)
- All amounts/transfers set to "0" (unlimited)
- Additive: sits alongside other collection approvals
- Do NOT use with: credit tokens (increment-only), soulbound tokens, subscription tokens

## Instructions

## Burnable Tokens

### Concept

A burnable approval allows any token holder to permanently destroy their tokens by transferring them to the burn address. This is useful for deflationary tokens, redemption systems, or any scenario where tokens should be removable from circulation.

### Burn Address

The burn address is the ETH null address (0x0000000000000000000000000000000000000000) converted to BitBadges format:
`bb1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqs7gvmv`

Tokens sent to this address are effectively destroyed — no one controls the private key.

### Required Approval Structure

Add this approval to `collectionApprovals`:

```json
{
  "fromListId": "!Mint",
  "toListId": "bb1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqs7gvmv",
  "initiatedByListId": "All",
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalId": "burnable-approval",
  "uri": "",
  "customData": "",
  "version": "0",
  "approvalCriteria": {
    "predeterminedBalances": {
      "manualBalances": [],
      "incrementedBalances": {
        "startBalances": [],
        "incrementTokenIdsBy": "0",
        "incrementOwnershipTimesBy": "0",
        "allowOverrideTimestamp": false,
        "recurringOwnershipTimes": { "startTime": "0", "intervalLength": "0", "chargePeriodLength": "0" },
        "allowOverrideWithAnyValidToken": false
      },
      "orderCalculationMethod": { "useOverallNumTransfers": false, "usePerToAddressNumTransfers": false, "usePerFromAddressNumTransfers": false, "usePerInitiatedByAddressNumTransfers": false, "useMerkleChallengeLeafIndex": false, "challengeTrackerId": "" }
    },
    "approvalAmounts": { "overallApprovalAmount": "0", "perToAddressApprovalAmount": "0", "perFromAddressApprovalAmount": "0", "perInitiatedByAddressApprovalAmount": "0", "amountTrackerId": "", "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" } },
    "maxNumTransfers": { "overallMaxNumTransfers": "0", "perToAddressMaxNumTransfers": "0", "perFromAddressMaxNumTransfers": "0", "perInitiatedByAddressMaxNumTransfers": "0", "amountTrackerId": "", "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" } },
    "coinTransfers": [],
    "merkleChallenges": [],
    "mustOwnTokens": [],
    "overridesFromOutgoingApprovals": false,
    "overridesToIncomingApprovals": true,
    "mustPrioritize": false
  }
}
```

### Key Fields Explained

- **fromListId: "!Mint"** — any holder (everyone except the Mint address) can burn
- **toListId: "bb1qqqqqqq..."** — destination is the burn address (a single-address list)
- **initiatedByListId: "All"** — anyone can initiate the burn (typically the holder themselves)
- **overridesToIncomingApprovals: true** — the burn address has no user-level incoming approvals, so this must override
- **approvalId: "burnable-approval"** — standard ID used by the frontend to detect burnability
- All amounts/transfers set to "0" (unlimited)

### Combining with Other Approvals

The burnable approval is additive — it sits alongside other collection approvals (mint approvals, transferable approvals, etc.). Order matters: place it after mint approvals but the system will match based on from/to addresses.

### When NOT to Use

- **Credit tokens**: These are increment-only by design; burning defeats the purpose
- **Soulbound tokens**: If tokens should be permanently bound to an address, don't add a burn approval
- **Subscription tokens**: Typically managed by the issuer, not burned by holders

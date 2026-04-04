# Address List

> On-chain address list where membership = owning x1 of token ID 1. Manager can add/remove addresses.

**Category:** Token Types

## Summary

Required standards: ["Address List"]

- validTokenIds: MUST be exactly [{ "start": "1", "end": "1" }]
- TWO collection approvals required with EXACT approvalIds (frontend depends on these):
  1. "manager-add": fromListId "Mint", toListId "All", initiatedByListId = creator. Mints token to add address.
  2. "manager-remove": fromListId "All", toListId burn address (bb1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqs7gvmv), initiatedByListId = creator. Burns token to remove address.
- BOTH approvals MUST have overridesFromOutgoingApprovals: true
- NO peer-to-peer transfer approval — only manager can modify the list
- Standard is "Address List" (NOT "Non-Transferable")
- Use build_address_list tool instead of build_token for this type

## Instructions

## Address List Configuration

An address list collection represents membership as token ownership: owning x1 of token ID 1 = being on the list. The manager controls membership by minting (adding) and burning (removing) tokens.

### CRITICAL: Exact Approval IDs Required

The frontend identifies address list approvals by their EXACT approvalIds. Using different IDs will break the UI.

- Approval 1: approvalId MUST be **"manager-add"**
- Approval 2: approvalId MUST be **"manager-remove"**

### Required Structure

1. **Standards**: ["Address List"] (NOT "Non-Transferable")
2. **validTokenIds**: [{ "start": "1", "end": "1" }]
3. **Burn address**: bb1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqs7gvmv (ETH null address in bb1 format)

### Manager-Add Approval (mint to add)

```json
{
  "fromListId": "Mint",
  "toListId": "All",
  "initiatedByListId": "bb1creator...",
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "tokenIds": [{ "start": "1", "end": "1" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "uri": "ipfs://METADATA_APPROVAL_manager-add",
  "customData": "",
  "approvalId": "manager-add",
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true
  },
  "version": "0"
}
```

### Manager-Remove Approval (forceful burn to remove)

```json
{
  "fromListId": "All",
  "toListId": "bb1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqs7gvmv",
  "initiatedByListId": "bb1creator...",
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "tokenIds": [{ "start": "1", "end": "1" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "uri": "ipfs://METADATA_APPROVAL_manager-remove",
  "customData": "",
  "approvalId": "manager-remove",
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true
  },
  "version": "0"
}
```

### Default Balances

```json
{
  "balances": [],
  "outgoingApprovals": [],
  "incomingApprovals": [],
  "autoApproveAllIncomingTransfers": true,
  "autoApproveSelfInitiatedOutgoingTransfers": true,
  "autoApproveSelfInitiatedIncomingTransfers": true,
  "userPermissions": {}
}
```

### Permissions

Lock approvals and token IDs:
- canUpdateCollectionApprovals: frozen
- canUpdateValidTokenIds: frozen
- canUpdateManager: frozen (typically)

## Common Mistakes

- DON'T use approvalId other than "manager-add" and "manager-remove" — the frontend depends on these exact strings.
- DON'T use standard "Non-Transferable" — address lists use "Address List".
- DON'T add a peer-to-peer transfer approval — only the manager should modify the list.
- DON'T use build_token — use build_address_list instead for this type.
- DON'T forget overridesFromOutgoingApprovals: true on BOTH approvals.

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

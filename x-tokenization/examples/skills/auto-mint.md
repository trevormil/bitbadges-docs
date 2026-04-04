# Auto-Mint

> Mint and distribute tokens to recipients at collection creation time using MsgTransferTokens

**Category:** Features

## Summary

Post-creation minting — adds MsgTransferTokens messages to the transaction so tokens are distributed immediately after collection creation.

- Transaction can contain MsgUniversalUpdateCollection PLUS one or more MsgTransferTokens messages
- All transfer messages use collectionId: "0" to reference the just-created collection
- prioritizedApprovals MUST always be specified (use [] if none needed)
- from: "Mint" for minting new tokens, bb1... address for peer-to-peer transfers
- The signing user (creator) is the initiator — collection must have an approval allowing this
- All numbers as strings — "1" not 1
- Prefer predeterminedBalances with x0 increments over maxNumTransfers for better frontend UX

When to use:
- User asks to mint tokens to themselves or others at creation time
- Initial distribution or pre-allocation of tokens
- Manager-only collections where the manager should receive all tokens immediately

When NOT to use:
- Public mint collections (users mint later via approval)
- Subscription collections (users mint on subscribe)
- Smart tokens (users deposit IBC coins to mint)
- Any collection where minting happens post-creation through approvals

## Instructions

## Auto-Mint — Post-Creation Transfers

A transaction can contain the collection creation message (MsgUniversalUpdateCollection) PLUS one or more MsgTransferTokens messages. All use collectionId: "0" to reference the just-created collection.

### MsgTransferTokens Structure

```json
{
  "typeUrl": "/tokenization.MsgTransferTokens",
  "value": {
    "creator": "bb1...",
    "collectionId": "0",
    "transfers": [{
      "from": "Mint",
      "toAddresses": ["bb1recipientaddress..."],
      "balances": [{
        "amount": "1",
        "tokenIds": [{ "start": "1", "end": "1" }],
        "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }]
      }],
      "prioritizedApprovals": [{
        "approvalId": "the-mint-approval-id",
        "approvalLevel": "collection",
        "approverAddress": "",
        "version": "0"
      }],
      "onlyCheckPrioritizedCollectionApprovals": false,
      "onlyCheckPrioritizedIncomingApprovals": false,
      "onlyCheckPrioritizedOutgoingApprovals": false,
      "memo": ""
    }]
  }
}
```

### When to add a transfer message
- User asks to mint tokens to themselves or others at creation time
- User wants initial distribution or pre-allocation of tokens
- Manager-only collections where the manager should receive all tokens immediately
- Any scenario where tokens should exist in wallets right after collection creation

### When NOT to add a transfer message
- Public mint collections (users mint later via the approval)
- Subscription collections (users mint on subscribe)
- Smart tokens (users deposit IBC coins to mint)
- Any collection where minting happens post-creation through approvals

### Critical transfer rules
1. **prioritizedApprovals MUST be specified** — even if empty []. Match the approvalId to one of the collection's collectionApprovals.
2. **from: "Mint"** for minting new tokens. Use a bb1... address for peer-to-peer transfers.
3. **The signing user (creator) is the initiator** — the collection must have an approval that allows this address as initiatedBy.
4. **collectionId: "0"** is auto-set — it references the collection created by message[0] in the same transaction.
5. **All numbers as strings** — "1" not 1.
6. **Prefer predeterminedBalances for one-time or fixed-use approvals** — Use incrementedBalances with x0 increments (incrementTokenIdsBy: "0", incrementOwnershipTimesBy: "0") instead of relying solely on maxNumTransfers. The frontend auto-detects predeterminedBalances and shows users the exact tokens they will receive, providing much better UX. Avoid manualBalances; incrementedBalances with x0 increments is preferred.

### Time-Dependent Ownership

For expiring tokens, calculate timestamps:
- Current time: use get_current_timestamp tool (milliseconds since epoch)
- Example: 5 minutes from now = current timestamp + (5 * 60 * 1000)

```json
"ownershipTimes": [{
  "start": "1706000000000",
  "end": "1706000300000"
}]
```

### Session-based patch operations
- add_transfer: { op: "add_transfer", transfer: { transfers: [...] } } — appends a MsgTransferTokens to the transaction
- remove_transfer: { op: "remove_transfer", index: 0 } — removes transfer message by index (0-based among transfer messages)
- update_transfer: { op: "update_transfer", index: 0, changes: {...} } — deep-merges changes into the transfer message

### Common mistakes
- DON'T forget to add prioritizedApprovals in MsgTransferTokens — even if empty ([]), this field must be present or the transfer fails.
- DON'T forget that the collection must have a mint approval that allows the creator as initiatedBy.
- DON'T add transfer messages for subscription, smart token, or public mint collections — minting happens post-creation through approvals.

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

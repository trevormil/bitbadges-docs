# Custom 2FA

> Two-factor authentication for transfers using a secondary approval address

**Category:** Token Types

## Summary

Required standards: ["Custom-2FA"]

- autoDeletionOptions.allowPurgeIfExpired: MUST be true
- Approval name MUST contain "Custom 2FA"
- Use time-dependent ownershipTimes in MsgTransferTokens (not forever)
- Calculate timestamps: current time + expiration duration (milliseconds since epoch)
- Tokens automatically expire and can be purged after expiration

## Instructions

## Custom-2FA Configuration

When creating a Custom-2FA collection, follow these requirements:

### Required Structure

1. **Standards**: MUST include "Custom-2FA"
   - "standards": ["Custom-2FA"]

2. **Approval Requirements**:
   - autoDeletionOptions.allowPurgeIfExpired: MUST be true
   - This allows expired tokens to be automatically purged
   - Approval name MUST contain "Custom 2FA"

3. **Time-Dependent Ownership**: Use time-dependent ownershipTimes in MsgTransferTokens
   - Calculate timestamps: current time + expiration duration
   - Example: 5 minutes = Date.now() + (5 * 60 * 1000)

### Complete Example

```json
{
  "messages": [
    {
      "typeUrl": "/tokenization.MsgUniversalUpdateCollection",
      "value": {
        "standards": ["Custom-2FA"],
        "collectionApprovals": [{
          "fromListId": "Mint",
          "toListId": "All",
          "initiatedByListId": "bb1manager...",
          "approvalId": "2fa-mint",
          "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
          "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
          "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
          "approvalCriteria": {
            "overridesFromOutgoingApprovals": true,
            "autoDeletionOptions": { "allowPurgeIfExpired": true }
          }
        }]
      }
    },
    {
      "typeUrl": "/tokenization.MsgTransferTokens",
      "value": {
        "collectionId": "0",
        "transfers": [{
          "from": "Mint",
          "toAddresses": ["bb1recipient..."],
          "balances": [{
            "amount": "1",
            "tokenIds": [{ "start": "1", "end": "1" }],
            "ownershipTimes": [{ "start": "1706000000000", "end": "1706000300000" }]
          }]
        }]
      }
    }
  ]
}
```

### 2FA-Specific Gotchas

- MUST set allowPurgeIfExpired: true
- Use time-dependent ownershipTimes in transfers (not forever)
- Calculate expiration timestamps correctly (milliseconds since epoch)
- Tokens automatically expire and can be purged after expiration

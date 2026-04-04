# Verified Gate

> Gate access based on BitBadges verified credential badges (collection ID 1)

**Category:** Features

## Summary

Gate access based on BitBadges verified credential badges (collection ID 1).

- Collection 1 is managed by BitBadges — you cannot mint badges in it
- Different token IDs = different verification types
- On-chain gate: use mustOwnTokens in approvalCriteria with collectionId: "1"
  - Set overrideWithCurrentTime: true for current ownership verification
  - Empty ownershipTimes = any ownership time qualifies
- Off-chain gate: use must-own-badges claim plugin with collectionId: 1
- Combine with other gates using $and/$or for multi-factor verification

## Instructions

## Verified Gate

Gate access based on BitBadges verified credential badges. Collection ID 1 contains verification attestation badges managed by the BitBadges team.

### How It Works

BitBadges collection 1 contains verification badges. Different token IDs represent different verification types. Users who have been verified own the corresponding badge. You can gate access by requiring ownership of specific verification badges.

### On-Chain Gate (mustOwnTokens in approvalCriteria)

```json
{
  "approvalCriteria": {
    "mustOwnTokens": [{
      "collectionId": "1",
      "amountRange": { "start": "1", "end": "18446744073709551615" },
      "tokenIds": [{ "start": "1", "end": "1" }],
      "ownershipTimes": [],
      "overrideWithCurrentTime": true
    }]
  }
}
```

### Off-Chain Gate (must-own-badges claim plugin)

```json
{
  "pluginId": "must-own-badges",
  "publicParams": {
    "ownershipRequirements": {
      "$and": [{
        "assets": [{
          "chain": "BitBadges",
          "collectionId": 1,
          "assetIds": [{ "start": 1, "end": 1 }],
          "mustOwnAmounts": { "start": 1, "end": 1 },
          "ownershipTimes": []
        }]
      }]
    }
  },
  "privateParams": {}
}
```

### Verified Gate Gotchas

- Collection ID 1 is managed by BitBadges — you cannot mint badges in it
- Token IDs in collection 1 correspond to different verification types
- Use overrideWithCurrentTime: true for on-chain checks to verify current ownership
- Empty ownershipTimes means any ownership time qualifies
- Combine with other gates using $and/$or for multi-factor verification

## Reference Collections

- [Collection 1](https://bitbadges.io/collections/1)

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

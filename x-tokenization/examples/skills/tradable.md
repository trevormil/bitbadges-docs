# Tradable NFTs

> NFT marketplace standard enabling peer-to-peer transfers with the "NFTMarketplace" standard tag and NFTPricingDenom

**Category:** Standards

## Summary

Required standards: ["NFTMarketplace", "NFTs", "NFTPricingDenom:ubadge"]

- MUST include all three standards together
- NFTPricingDenom format: "NFTPricingDenom:<denom>" (sets pricing denomination for orderbook)
- MUST include a free transfer approval: fromListId: "!Mint", toListId: "All", initiatedByListId: "All", approvalId: "transferable-approval"
- Enables orderbook/marketplace integration
- Typically used with NFT collections
- Note: Legacy names "Tradable" and "DefaultDisplayCurrency" are still accepted for existing collections

## Instructions

## Tradable NFTs Configuration

When enabling trading for NFTs, follow these requirements:

### Required Structure

1. **Standards**: MUST include "NFTMarketplace", "NFTs", and "NFTPricingDenom:ubadge"
   - "standards": ["NFTMarketplace", "NFTs", "NFTPricingDenom:ubadge"]
   - NFTPricingDenom sets the pricing denomination for orderbook display
   - Replace "ubadge" with your desired currency denom if different

2. **Free Transfer Approval**: Include a default collection approval for peer-to-peer transfers
   ```json
   {
     "fromListId": "!Mint",
     "toListId": "All",
     "initiatedByListId": "All",
     "approvalId": "transferable-approval",
     "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
     "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
     "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }]
   }
   ```

### Complete Example

```json
{
  "updateStandards": true,
  "standards": ["NFTMarketplace", "NFTs", "NFTPricingDenom:ubadge"]
}
```

### Tradable Gotchas

- MUST include all three standards together
- NFTPricingDenom format: "NFTPricingDenom:denom"
- This enables orderbook/marketplace integration
- Typically used with NFT collections
- The currency denom determines how prices are displayed in marketplaces
- Legacy names "Tradable" and "DefaultDisplayCurrency" still work for existing collections

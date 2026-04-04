# Liquidity Pools

> Liquidity pool standard with the "Liquidity Pools" protocol standard tag

**Category:** Standards

## Summary

Required standards: ["Liquidity Pools"]

- MUST set invariants.disablePoolCreation: false
- MUST configure at least one alias path (required for liquidity pools to function)
- Merkle challenges are NOT compatible with liquidity pools
- Enables decentralized exchange (DEX) trading interfaces

## Instructions

## Liquidity Pools Configuration

When enabling liquidity pools for a collection, follow these requirements:

### Required Structure

1. **Standards**: MUST include "Liquidity Pools"
   - "standards": ["Liquidity Pools"]

2. **Invariants**: MUST set disablePoolCreation to false
   ```json
   {
     "standards": ["Liquidity Pools"],
     "invariants": {
       "disablePoolCreation": false
     }
   }
   ```

3. **Alias Paths**: MUST configure at least one alias path
   - This is REQUIRED for liquidity pools to function
   - Use the alias path configuration provided in the skill config
   ```json
   {
     "aliasPathsToAdd": [{
       "denom": "uvatom",
       "symbol": "uvatom",
       "conversion": {
         "sideA": { "amount": "1" },
         "sideB": [{ "amount": "1", "tokenIds": [{ "start": "1", "end": "1" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }]
       },
       "denomUnits": [{
         "decimals": "6",
         "symbol": "vATOM",
         "isDefaultDisplay": true,
         "metadata": { "uri": "", "customData": "", "image": "https://example.com/token-logo.png" }
       }],
       "metadata": { "uri": "", "customData": "", "image": "https://example.com/token-logo.png" }
     }]
   }
   ```

### Liquidity Pools Gotchas

- disablePoolCreation MUST be false (not true)
- MUST configure at least one alias path (required for liquidity pools)
- All alias path and denomUnit metadata MUST include an `image` field with a valid URL (token logo)
- Merkle challenges are NOT compatible with liquidity pools
- This enables decentralized exchange (DEX) trading interfaces

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

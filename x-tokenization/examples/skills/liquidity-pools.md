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
         "metadata": { "uri": "ipfs://METADATA_ALIAS_uvatom_UNIT", "customData": "" }
       }],
       "metadata": { "uri": "ipfs://METADATA_ALIAS_uvatom", "customData": "" }
     }]
   }
   ```

### Liquidity Pools Gotchas

- disablePoolCreation MUST be false (not true)
- MUST configure at least one alias path (required for liquidity pools)
- PathMetadata (on every aliasPath and denomUnit) is ONLY `{ uri, customData }`. Put the token logo in the off-chain JSON at the placeholder URI and register a matching entry in `metadataPlaceholders` — never put `image` on the proto.
- Merkle challenges are NOT compatible with liquidity pools
- This enables decentralized exchange (DEX) trading interfaces

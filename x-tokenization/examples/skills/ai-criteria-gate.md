# AI Criteria Gate

> AI-evaluated criteria gate using attestation NFTs and dynamic store for automated access decisions

**Category:** Features

## Summary

AI agent evaluates criteria and gates access via attestation NFTs or dynamic store entries.

- Approach 1 (Attestation NFT): AI agent is sole minter of non-transferable NFTs; gate access via BB-402 / must-own-badges
  - Non-transferable: no transferable approval + locked canUpdateCollectionApprovals
  - On-chain proof, composable, but requires gas per attestation
- Approach 2 (Dynamic Store): AI agent writes to off-chain dynamic store; claims use whitelist plugin with useDynamicStore: true
  - No gas per evaluation, but off-chain and not composable on-chain
- Store AI agent address in collection.customData (JSON)

## Instructions

## AI Criteria Gate

An AI Criteria Gate uses an AI agent to evaluate whether users meet certain criteria, then gates access via attestation NFTs or dynamic store entries.

### Two Approaches

#### Approach 1: Attestation NFT
The AI agent mints a non-transferable NFT (attestation badge) to users who pass evaluation. Access is then gated on owning that badge.

**Setup:**
1. Create an NFT collection with the AI agent as the sole minter (initiatedByListId: agent's address)
2. Make it non-transferable (no transferable approval, locked canUpdateCollectionApprovals)
3. AI agent evaluates criteria → mints badge to qualifying users
4. Gate access using BB-402 / must-own-badges on the attestation collection

**Pros:** On-chain proof, composable, works with any badge-gated system
**Cons:** Requires transaction per attestation, gas costs

#### Approach 2: Dynamic Store
The AI agent writes qualifying addresses to an off-chain dynamic store. Claims use the whitelist plugin with useDynamicStore: true.

**Setup:**
1. Create a dynamic store via the developer portal
2. AI agent evaluates criteria → writes qualifying addresses to the store
3. Use whitelist plugin with useDynamicStore: true and the store's dynamicDataId
4. Users claim through the off-chain claim flow

**Pros:** No gas per evaluation, instant updates, flexible
**Cons:** Off-chain (relies on BitBadges infrastructure), not composable on-chain

### Combining Both

For maximum flexibility:
1. AI agent evaluates criteria
2. Writes to dynamic store for immediate access (claims)
3. Mints attestation NFT for permanent on-chain proof
4. Different resources can check either or both

### Implementation

1. Create the attestation collection and/or dynamic store
2. Configure the AI agent with evaluation criteria
3. Store the agent's address in collection.customData (JSON)
4. Wire up BB-402 or claim plugins to check ownership/store membership

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

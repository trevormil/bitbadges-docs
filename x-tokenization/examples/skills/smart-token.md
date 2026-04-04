# Smart Token

> IBC-backed smart token with 1:1 backing and two required approvals (backing + unbacking)

**Category:** Token Types

## Summary

Required standards: ["Smart Token"]

- MUST include cosmosCoinBackedPath in invariants with conversion sideA/sideB
- MUST configure at least one alias path (decimals must match IBC denom decimals)
- MUST create TWO required collection approvals (backing + unbacking). Transferable approval is COMMON but OPTIONAL:
  1. Backing approval (REQUIRED): fromListId = backing address, allowBackedMinting: true, mustPrioritize: true
  2. Transferable approval (OPTIONAL — include for wrapped assets, omit for vaults/escrows): fromListId = "!Mint", toListId = "All"
  3. Unbacking approval (REQUIRED): toListId = backing address, allowBackedMinting: true, mustPrioritize: true
- DO NOT use fromListId: "Mint" — tokens are created via IBC backing, not traditional minting
- Backing addresses are protocol-controlled with auto-set approvals — overridesFromOutgoingApprovals is irrelevant for backing/unbacking (leave unset or false)
- noForcefulPostMintTransfers invariant should be TRUE — smart tokens do NOT need forceful transfer overrides
- Unbacking fromListId uses "!Mint:backingAddress" syntax (excludes both Mint and backing address — meaning only regular holders can unback)
- Backing address is deterministic — use generate_backing_address tool
- Optional: Add "AI Agent Vault" to standards for AI Prompt tab (display-only)
- Alias path: symbol = base unit (e.g. "uvatom"), denomUnits = display units with decimals > 0 only, each denomUnit MUST have metadata with an image
- All alias path and denomUnit metadata MUST include an `image` field (token logo URL)

## Instructions

## Smart Token Configuration

### Mental Model: Three Phases

Think about smart tokens in three distinct phases:

1. **Phase 1 — Deposits (Backing)**: Users send IBC coins (e.g. USDC) to the backing address and receive wrapped tokens 1:1. This is the on-ramp.
2. **Phase 2 — Transferability (While Backed)**: While tokens exist in the wrapped silo, can users transfer them peer-to-peer? This is the key design decision — transferable for wrapped assets, non-transferable for vaults/escrows.
3. **Phase 3 — Withdrawals (Unbacking)**: Users send wrapped tokens back to the backing address and receive their IBC coins 1:1. This is the off-ramp. Rate limits, 2FA, and other controls go here.

Each phase maps to at least one collection approval. Design each phase independently — they are orthogonal concerns.

### Required Structure

1. **Standards**: MUST include "Smart Token"
   - "standards": ["Smart Token"]

2. **Invariants**: MUST include cosmosCoinBackedPath
   ```json
   {
     "standards": ["Smart Token"],
     "invariants": {
       "cosmosCoinBackedPath": {
         "conversion": {
           "sideA": {
             "amount": "1",
             "denom": "ibc/A4DB47A9D3CF9A068D454513891B526702455D3EF08FB9EB558C561F9DC2B701"
           },
           "sideB": [{
             "amount": "1",
             "tokenIds": [{ "start": "1", "end": "1" }],
             "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }]
           }]
         }
       }
     }
   }
   ```

3. **Alias Paths**: MUST configure at least one alias path
   - The alias path decimals MUST match the IBC denom's decimals
   - This is REQUIRED for Smart Tokens to function properly
   - Use the alias path configuration provided in the skill config
   - Alias path denom and symbol MUST only contain a-zA-Z, _, {, }, and - characters. NEVER use the raw IBC denom (ibc/...) as the alias path denom — create a new symbol like "wuusdc" or "uwrapped"
   - Do NOT reuse reserved symbols (USDC, ATOM, BADGE, etc.) — always prefix with "w" or similar (e.g., wUSDC, wATOM)

### Approval System

Smart Tokens require TWO approvals (backing + unbacking). A third transferable approval is common for wrapped assets but optional for vaults/escrows:

#### 1. Backing Approval (for backing tokens)
This approval allows tokens to be sent FROM the IBC backing address (backing the tokens).

```json
{
  "fromListId": "bb1backingaddress...",
  "toListId": "!bb1backingaddress...",
  "initiatedByListId": "All",
  "approvalId": "smart-token-backing",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "mustPrioritize": true,
    "allowBackedMinting": true
  }
}
```
Note: Backing addresses are protocol-controlled with auto-set approvals. overridesFromOutgoingApprovals is irrelevant and can be omitted.

#### 2. Transferable Approval (OPTIONAL — for peer-to-peer transfers)
This approval allows tokens to be transferred between users (non-backing addresses). Include for wrapped assets where holders need to trade/transfer. Omit for simple deposit/withdraw vaults or escrows where tokens should not move between users.

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

#### 3. Unbacking Approval (for unbacking tokens)
This approval allows tokens to be sent TO the IBC backing address (unbacking the tokens).
The `!Mint:bb1backingaddress...` fromListId uses colon-separated exclude syntax — it means "everyone except Mint and the backing address", so only regular holders can unback.

```json
{
  "fromListId": "!Mint:bb1backingaddress...",
  "toListId": "bb1backingaddress...",
  "initiatedByListId": "All",
  "approvalId": "smart-token-unbacking",
  "tokenIds": [{ "start": "1", "end": "1" }],
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalCriteria": {
    "mustPrioritize": true,
    "allowBackedMinting": true
  }
}
```

### Multi-Message Deposit/Withdraw Pattern

For safety, backing approvals enforce that the initiator must be the recipient (deposit) and the initiator must be the sender (withdraw). To deposit to or withdraw for another address, use a multi-message transaction:

#### Deposit to Another Address (2 msgs in one tx)
1. **MsgTransferTokens**: Deposit to self (from: backingAddress, to: self)
2. **MsgTransferTokens**: Transfer from self to target (from: self, to: targetAddress) — uses the transferable approval

#### Withdraw for Another Address (2 msgs in one tx)
1. **MsgTransferTokens**: Withdraw to self (from: self, to: backingAddress)
2. **MsgSend** (cosmos bank): Send equivalent IBC coins from self to target address

### Optional: AI Agent Vault Standard

Add "AI Agent Vault" to the standards array to enable an AI Prompt tab in the frontend. This is **display-only** and has no impact on on-chain logic.

```json
"standards": ["Smart Token", "AI Agent Vault"]
```

### Optional: DEX Tradability (Liquidity Pools)

Most smart tokens are NOT tradable on DEX — only add this if the user explicitly requests trading/DEX functionality:
- Add "Liquidity Pools" to standards
- Set invariants.disablePoolCreation to **false** (override the safe default)
- MUST have at least one alias path configured
- MUST have a transferable approval (peer-to-peer transfers required for trading)

### IBC Backed Minting Rules

- **Backing/unbacking approvals**: Backing addresses are protocol-controlled with auto-set approvals — overridesFromOutgoingApprovals is **irrelevant** (leave unset or false). noForcefulPostMintTransfers should be TRUE.
- **Unbacking fromListId**: Use `!Mint:backingAddress` syntax — colon-separated addresses with `!` prefix means "everyone except Mint and backing address" (only regular holders can unback)
- **Use allowBackedMinting: true** for IBC backed operations
- **Use mustPrioritize: true** (required, not compatible with auto-scan)
- **Backing Address**: Use the backing address as fromListId (NOT "Mint") — generated deterministically from the IBC denom via generate_backing_address tool

### Alias Path Configuration

MUST configure at least one alias path. Structure:
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
      "metadata": { "uri": "ipfs://...", "customData": "" }
    }],
    "metadata": { "uri": "", "customData": "", "image": "https://example.com/token-logo.png" }
  }]
}
```

Rules:
- symbol = base unit symbol (e.g., "uvatom")
- denomUnits = display units with decimals > 0 ONLY (base decimals 0 is implicit)
- Each denomUnit MUST have metadata with an image field (often the same image as the base alias path)
- The alias path itself MUST have metadata with an image field — this is the token logo shown in the UI
- isDefaultDisplay: true for the primary display unit
- **CRITICAL**: All metadata objects (alias path, denomUnits, cosmosCoinWrapperPaths) MUST include an `image` field with a valid URL. Missing images will be auto-fixed to a default but you should always provide a descriptive image.

### Cosmos Coin Wrapper (Optional)

For wrapping native Cosmos SDK coins, use `allowSpecialWrapping: true` and `cosmosCoinWrapperPathsToAdd`:
```json
{
  "cosmosCoinWrapperPathsToAdd": [{
    "denom": "uatom",
    "symbol": "uatom",
    "conversion": {
      "sideA": { "amount": "1" },
      "sideB": [{ "amount": "1", "tokenIds": [{ "start": "1", "end": "1" }], "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }] }]
    },
    "denomUnits": [{ "decimals": "6", "symbol": "ATOM", "isDefaultDisplay": true, "metadata": { "uri": "", "customData": "", "image": "https://example.com/token-logo.png" } }],
    "metadata": { "uri": "", "customData": "", "image": "https://example.com/token-logo.png" },
    "allowOverrideWithAnyValidToken": false
  }]
}
```

### Optional: Unbacking Withdraw Limits

Add approvalAmounts to the unbacking approval to enforce daily or total withdraw limits:

```json
{
  "approvalCriteria": {
    "mustPrioritize": true,
    "allowBackedMinting": true,
    "overridesFromOutgoingApprovals": false,
    "approvalAmounts": {
      "overallApprovalAmount": "0",
      "perFromAddressApprovalAmount": "1000000",
      "perToAddressApprovalAmount": "0",
      "perInitiatedByAddressApprovalAmount": "0",
      "amountTrackerId": "daily-withdraw-limit",
      "resetTimeIntervals": { "startTime": "0", "intervalLength": "86400000" }
    }
  }
}
```

- **Daily limit**: Use perFromAddressApprovalAmount with resetTimeIntervals.intervalLength: "86400000" (24 hours in ms)
- **Total limit**: Use overallApprovalAmount with intervalLength: "0" (no reset)
- amountTrackerId must be unique per approval

### Optional: 2FA on Unbacking

Require ownership of a 2FA token to withdraw. Add mustOwnTokens to the unbacking approval:

```json
{
  "approvalCriteria": {
    "mustPrioritize": true,
    "allowBackedMinting": true,
    "overridesFromOutgoingApprovals": false,
    "mustOwnTokens": [{
      "collectionId": "74",
      "amountRange": { "start": "1", "end": "18446744073709551615" },
      "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
      "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
      "overrideWithCurrentTime": true,
      "mustSatisfyForAllAssets": false,
      "ownershipCheckParty": "initiator"
    }]
  }
}
```

- collectionId: the 2FA collection ID
- overrideWithCurrentTime: true ensures the check uses the current time (important for expiring 2FA tokens)
- ownershipCheckParty: "initiator" checks the person initiating the withdrawal

### Metadata Guidance

- Collection metadata, token metadata, alias path metadata, and denomUnit metadata should all have descriptive names/descriptions and images
- Do NOT use lazy placeholder names like "Backing Approval" — write real user-facing descriptions explaining what each approval does

### Key Rules Summary

1. **NO fromListId: "Mint" approvals**: Tokens are created via IBC backing, not traditional minting
2. **Use allowBackedMinting: true** in both backing and unbacking approvals
3. **Use mustPrioritize: true** (required for IBC backed operations)
4. **Backing approval**: overridesFromOutgoingApprovals: true is RECOMMENDED (backing addresses auto-set their approvals, so it works either way, but true is good practice). **Unbacking approval**: MUST be false (sender is a regular user)
5. **Unbacking fromListId**: Use `!Mint:backingAddress` syntax — excludes both Mint and backing address so only regular holders can send tokens back
6. **MUST create backing + unbacking approvals**. Transferable approval is common but optional (omit for vaults/escrows)
7. **MUST configure alias path** with matching decimals
8. The backing address is deterministic — use the one from generate_backing_address tool

## Common Mistakes

- DON'T use fromListId: "Mint" — smart tokens are created via IBC backing, not traditional minting. Use the backing address as fromListId instead.
- DON'T forget mustPrioritize: true on backing and unbacking approvals — without it, the chain cannot match the approval and the transfer fails.
- DON'T assume all three approvals are required — the transferable approval is optional. Omit it for vaults or escrows where tokens should not move between users.
- DON'T use "All" as fromListId or toListId for backing/unbacking operations — use the exact deterministic backing address from generate_backing_address.
- DON'T use number types for amounts or IDs — all values must be strings ("1" not 1, "18446744073709551615" not Number.MAX_SAFE_INTEGER).
- DON'T forget to configure an alias path — smart tokens will not function without one, and the alias decimals must match the IBC denom decimals.

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

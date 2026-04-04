# Transferability & Update Rules

> Lock collection permissions to make properties permanently immutable or permanently permitted

**Category:** Advanced

## Summary

Controls whether collection properties can be changed after creation.

- Two states: FROZEN (permanentlyForbiddenTimes: FOREVER) or NEUTRAL (empty [])
- NEUTRAL [] = manager can update now AND can freeze it later. Use this for editable fields.
- FROZEN = permanent and irreversible. Use for fields that should never change.
- AVOID permanentlyPermittedTimes — it permanently prevents locking. Almost never needed.
- canUpdateCollectionApprovals: controls transfer rule mutability
  - SECURITY: If manager can update Mint approvals, they can mint unlimited tokens
  - Default to frozen unless user requests updatable
- List IDs in permissions: ONLY use reserved IDs ("All", "Mint", "!Mint", direct "bb1..." addresses)
- permanentlyForbiddenTimes: [{ "start": "1", "end": "18446744073709551615" }] = frozen forever

## Instructions

## Transferability & Update Rules Configuration

When configuring collection permissions for transferability and update rules, you MUST follow these critical requirements:

### Critical Permission Rules

**canUpdateCollectionApprovals**:
   - **CRITICAL**: Controls whether transfer rules (approvals) can be changed after creation
   - **SECURITY RISK**: If the manager can update approvals from the "Mint" address, they can mint any amount
   - **DEFAULT**: Should be **forbidden (frozen)** for collections where transfer rules should be locked
   - **Format**: Uses CollectionApprovalPermission format (see below)

### CollectionApprovalPermission Format

```json
{
  "fromListId": "All",
  "toListId": "All",
  "initiatedByListId": "All",
  "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
  "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
  "approvalId": "All",
  "permanentlyPermittedTimes": [],
  "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }]
}
```

**Key Fields:**
- **List IDs** (fromListId, toListId, initiatedByListId): Use ONLY reserved list IDs:
  - "All" — Any address
  - "Mint" — Mint address
  - "!Mint" — Everything except Mint
  - "bb1..." — Direct address
  - "!bb1..." — Everything except the specific address
  - "bb1abc:bb1xyz" — Colon-separated addresses
  - **DO NOT USE**: Custom list IDs — only reserved IDs or direct addresses
- **approvalId**: "All" to restrict all approvals, or a specific approvalId string

### Example: Immutable Transfer Rules (All Frozen)

```json
{
  "collectionPermissions": {
    "canUpdateCollectionApprovals": [{
      "fromListId": "All",
      "toListId": "All",
      "initiatedByListId": "All",
      "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
      "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
      "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
      "approvalId": "All",
      "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }],
      "permanentlyPermittedTimes": []
    }]
  }
}
```

### Example: Restricting Only Mint Approvals

```json
{
  "canUpdateCollectionApprovals": [{
    "fromListId": "Mint",
    "toListId": "All",
    "initiatedByListId": "All",
    "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
    "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "approvalId": "All",
    "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "permanentlyPermittedTimes": []
  }]
}
```

### Example: Restricting Specific Approval ID

```json
{
  "canUpdateCollectionApprovals": [{
    "fromListId": "All",
    "toListId": "All",
    "initiatedByListId": "All",
    "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "tokenIds": [{ "start": "1", "end": "18446744073709551615" }],
    "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "approvalId": "mint-approval",
    "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "permanentlyPermittedTimes": []
  }]
}
```

### Empty Permission Arrays — CRITICAL

**IMPORTANT**: If a permission entry has both `permanentlyPermittedTimes` and `permanentlyForbiddenTimes` as empty arrays, the entire permission entry is redundant and should be replaced with an empty array.

**For ActionPermission** (canDeleteCollection, canArchiveCollection, etc.):
- Wrong: `"canArchiveCollection": [{ "permanentlyPermittedTimes": [], "permanentlyForbiddenTimes": [] }]`
- Correct: `"canArchiveCollection": []`

**For TokenIdsActionPermission** (canUpdateTokenMetadata, canUpdateValidTokenIds):
- Wrong: `"canUpdateTokenMetadata": [{ "tokenIds": [...], "permanentlyPermittedTimes": [], "permanentlyForbiddenTimes": [] }]`
- Correct: `"canUpdateTokenMetadata": []`

**For CollectionApprovalPermission** (canUpdateCollectionApprovals):
- If both time arrays are empty, use empty array: `"canUpdateCollectionApprovals": []`

### Permission Presets

Three common permission configurations:

**1. Fully Immutable** — Everything frozen. Nothing can change after creation.
- ALL permissions set to permanentlyForbiddenTimes: FOREVER
- Use when: the collection should never change

**2. Manager Controlled** — Manager can change everything except delete.
- canDeleteCollection: frozen
- Everything else: NEUTRAL [] (editable now, can be frozen later)
- Use when: the manager needs full control (issuer-controlled tokens, evolving collections)

**3. Locked Approvals (recommended default)** — Approvals and supply frozen, metadata editable.
- canDeleteCollection: frozen
- canUpdateStandards: frozen
- canUpdateManager: frozen
- canUpdateValidTokenIds: frozen
- canUpdateCollectionApprovals: frozen
- canUpdateCollectionMetadata: NEUTRAL []
- canUpdateTokenMetadata: NEUTRAL []
- canArchiveCollection: NEUTRAL []
- canUpdateCustomData: NEUTRAL []
- Use when: supply and rules should be immutable but metadata needs updates

**Understanding permission states:**
- Empty array [] = NEUTRAL (manager can update now AND can lock it later — preserves maximum flexibility)
- permanentlyForbiddenTimes: FOREVER = FROZEN (can never be changed — permanent and irreversible)
- permanentlyPermittedTimes: FOREVER = PERMANENTLY ALLOWED (can never be frozen — almost never needed)

**IMPORTANT**: For editable fields, ALWAYS use NEUTRAL (empty []) instead of permanentlyPermittedTimes. Neutral gives the same current behavior (manager can update) but preserves the option to freeze it later. Only use permanentlyPermittedTimes if the user explicitly requests a guarantee that a field can never be locked.

### Security Considerations

- **Mint Transfer Rules**: If canUpdateCollectionApprovals is allowed for Mint, the manager could mint unlimited tokens
- **Post-Mint Transfer Rules**: If post-mint transfer rules can be updated, the manager could change transferability
- **Best Practice**: Default to locked (frozen) transfer rules unless user explicitly requests updatable rules
- Only allow updates for dynamic collections where the user explicitly requests flexibility

## Common Mistakes

- DON'T use custom list IDs in permissions — only reserved IDs: "All", "Mint", "!Mint", or direct bb1... addresses.
- DON'T leave both permanentlyPermittedTimes and permanentlyForbiddenTimes as empty arrays in a permission entry — this is redundant. Replace the entire entry with an empty array [].
- DON'T forget that unfrozen Mint approval permissions means the manager can mint unlimited tokens — freeze canUpdateCollectionApprovals for Mint if supply should be fixed.
- DON'T confuse empty permission array [] (neutral/unset) with a frozen permission — empty means the field is still updatable.

---

*Auto-generated from [bitbadges-builder-mcp](https://github.com/bitbadges/bitbadges-builder-mcp) skill instructions. Do not edit manually — run `npx tsx scripts/gen-skill-docs.ts` to regenerate.*

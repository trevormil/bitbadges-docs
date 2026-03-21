# Token Security Model: Default-Deny, Version Enforcement, and Permanent Permission Locks

This page explains the four-layer security model that BitBadges uses to protect token transfers. Unlike most token standards where everything is allowed unless restricted, BitBadges starts from a position where nothing is allowed and you build up from there.

## Layer 1: Default-Deny

Every transfer is denied by default. Nothing moves unless it explicitly matches a configured approval with the right criteria: time window, amount cap, address list, ownership requirement, or any other condition you set.

This is not a feature you opt into. It is the base state of every collection. You build up from "nothing allowed" rather than patching holes in "everything allowed."

**What this means in practice:** If you create a collection and do not configure any approvals, no tokens can be transferred. You must explicitly define every transfer path.

```typescript
// A collection with no approvals = no transfers possible
const collection = {
  collectionApprovals: []
  // No approvals configured -> all transfers are denied
};
```

## Layer 2: Version-Enforced Approvals

Every approval has a version number. When a user references an approval in their transfer, the version must match exactly. If the version is wrong, the transfer fails immediately with a hard error.

There is no silent fallback to a different approval. There is no ambiguity about which rules applied to a given transfer.

```typescript
// The transfer must reference the exact approval version
const transfer = {
  prioritizedApprovals: [{
    approvalId: "transferable",
    approvalLevel: "collection",
    version: "3"  // Must match the current version exactly
  }]
};
```

**Why this matters:** If the collection manager updates an approval (changing its criteria), all pending transfers that reference the old version will fail rather than silently applying the new rules. Users always know exactly which rules they are agreeing to.

## Layer 3: MustPrioritize

Approvals that involve coin transfers, merkle challenges, or predetermined balances are automatically flagged as `mustPrioritize`. This means you must explicitly reference them by ID and version in your transfer -- the chain will not auto-scan for them.

The chain enforces this flag at collection creation. You cannot accidentally create a collection where critical approvals can be silently skipped.

```typescript
// Critical approvals require explicit reference
const approval = {
  approvalId: "payment-gated-mint",
  mustPrioritize: true,  // Enforced automatically for coin transfers
  approvalCriteria: {
    coinTransfers: [{ /* ... */ }]
  }
};
```

For more on how auto-scan and prioritized approvals interact, see [Auto-Scan vs. Prioritized Approvals](../../learn/auto-scan-and-prioritized-approvals.md).

## Layer 4: Permanent Permission Locks

Permissions in BitBadges can be permanently locked in two directions:

- **`permanentlyForbiddenTimes`** -- "After this point, this permission can never be granted." The manager can never modify the locked property, regardless of any future action.
- **`permanentlyPermittedTimes`** -- "This permission is guaranteed to remain available forever." The manager cannot revoke it.

Once set, no admin key, governance vote, or chain upgrade can change these locks. They are enforced at the protocol level.

```typescript
const permissions = {
  canUpdateCollectionApprovals: [{
    // Lock the transferable approval so it can never be changed
    permanentlyForbiddenTimes: [{
      start: "1",
      end: "18446744073709551615"
    }],
    approvalId: "transferable",
    tokenIds: [{ start: "1", end: "1" }],
    ownershipTimes: [{ start: "1", end: "18446744073709551615" }]
  }]
};
```

**Why this matters for real assets:** A security token needs to prove that its transfer restrictions cannot be modified. Not "our multisig promises not to change them" -- actual cryptographic proof that the rules are immutable from a specific point onward. Permanent permission locks provide this guarantee.

## The Four Layers Together

| Layer | What It Does | Failure Mode Without It |
|-------|-------------|------------------------|
| Default-deny | Nothing moves without explicit approval | Unintended transfers through uncovered paths |
| Version enforcement | Transfers must reference exact approval version | Users unknowingly accept changed rules |
| MustPrioritize | Critical approvals cannot be silently skipped | Coin transfers or merkle challenges bypassed by auto-scan |
| Permanent locks | Rules become provably immutable | Admin keys can change any rule at any time |

Each layer addresses a different class of vulnerability. Together, they provide a security model where token behavior is fully deterministic and auditable.

## Related Pages

- [Manager / Permissions](../../learn/permissions.md) -- how the permission system works
- [Transferability / Approvals](../../learn/transferability.md) -- how approvals control token movement
- [Auto-Scan vs. Prioritized Approvals](../../learn/auto-scan-and-prioritized-approvals.md) -- how the chain resolves which approval applies
- [Building Collection Permissions](../../x-tokenization/examples/building-collection-permissions.md) -- practical examples of permission configurations

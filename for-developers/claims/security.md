# Security & Trust Assumptions

Claims are an off-chain system managed by BitBadges. Understanding the trust model is important, especially when claims gate on-chain actions.

## Claim Creator Authority

The claim creator controls which plugins are used and how criteria are evaluated. They can update claim configuration at any time — add/remove plugins, change params, modify success logic. Each update increments the claim's `version`.

When completing claims via API, pass `_expectedVersion` to guard against silent changes:

```typescript
await BitBadgesApi.completeClaim(claimId, address, {
  _expectedVersion: '0',  // Fails if the claim has been updated
  'password-instance': { password: 'secret' }
});
```

If the version doesn't match, the call fails rather than silently using new criteria. Obtain the current version from `getClaim()`. Pass `_expectedVersion: '-1'` to skip the check (not recommended).

## BitBadges as Oracle

BitBadges servers evaluate off-chain criteria, manage claim state, and (when gating on-chain approvals) distribute Merkle proofs. You trust BitBadges to:

- Honestly check plugin criteria
- Correctly issue proofs only to eligible users
- Not distribute proofs to ineligible users
- Accurately maintain claim state (attempt counts, used codes, etc.)

When [gating on-chain approvals](gating-approvals.md), the on-chain Merkle verification ensures proofs are **structurally valid** but cannot verify that the off-chain criteria were applied correctly.

## Third-Party Plugin Dependencies

If your claim uses custom plugins hosted by third parties, you trust those endpoints to be:

- **Honest** — approving only eligible users
- **Available** — responding within the 10-second timeout
- **Secure** — not compromised or manipulated

A compromised plugin could approve ineligible users or deny eligible ones. Fewer plugins = fewer trust dependencies.

## Code & Password Distribution

If using claim codes, whoever distributes the codes controls access. Leaked or stolen codes grant claim access regardless of the intended recipient.

For on-chain gating, the Merkle proof leaf signature binds the proof to a specific address, so a leaked code can only be used by the address that claimed it. But the claim itself (off-chain step) can be completed by anyone with the code.

Passwords have the same risk — anyone with the password can attempt the claim. Use passwords primarily for backend auto-completion flows where only your server knows the password.

## On-Chain Gating Risks

When claims gate on-chain approvals, additional risks arise from the hybrid off-chain/on-chain architecture. See [Gating On-Chain Approvals — Consistency Requirements](gating-approvals.md#consistency-requirements) for misalignment scenarios and best practices.

Key risks:

- **Misaligned limits** — claim allows more users than the Merkle tree has leaves
- **Stale proofs** — on-chain approval updated without updating the claim
- **Time window mismatch** — claim and on-chain `transferTimes` don't align

## Minimizing Trust

For high-stakes use cases:

- **Use on-chain-only criteria** where possible (token ownership, on-chain dynamic store challenges)
- **Use claims as a convenience layer** where the on-chain approval criteria independently enforces constraints
- **Design for failure** — rollbacks, snapshots, or other mechanisms to handle off-chain trust breaches
- **Audit your plugin pipeline** — review what each plugin does and who controls it
- **Monitor claim activity** via the API to detect anomalies

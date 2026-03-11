# Overview

Claims follow a simple model: **Meet criteria → Receive rewards.** They are composed of one or more plugins executed in order. By default all must pass, but you can configure AND/OR/NOT logic (see [success logic](concepts.md#success-logic)).

## How Claims Work

1. A claim is created with a set of plugins (built-in / BitBadges-provided or custom)
2. A user attempts to claim (in-site, via API, or programmatically)
3. All plugins are executed **in parallel** — each receives the user's address, parameters, and returns success/failure independently
4. If the configured success logic is satisfied for the group of plugins (typically all must pass), the claim succeeds
5. Rewards can be distributed (token mints, gated content, points, etc.) based on the claim success

## Indexed vs Non-Indexed Claims

**Indexed claims** (standard) include a `numUses` plugin and track every successful attempt with an incrementing claim number (#0, #1, #2, ...). They maintain a permanent ledger of who claimed and when, and support usage limits, per-address tracking, and claim number assignment.

**On-demand claims** (non-indexed) have no `numUses` plugin. They evaluate criteria in real-time without tracking individual attempts — useful for stateless checks like token ownership. Results are cached according to the claim's [cache policy](concepts.md#cache-policy-on-demand-claims).

Most claims are indexed. On-demand claims are for scenarios where you just need a real-time yes/no eligibility check without recording state.

## Async Processing

Claim completion is **asynchronous**. When you call `completeClaim()`:

1. BitBadges auto-simulates first (instant validation)
2. If simulation passes, the claim is added to a **processing queue**
3. You receive a `claimAttemptId` immediately
4. The claim processes in the queue (typically 1-5 seconds)
5. Poll `getClaimAttemptStatus(claimAttemptId)` for the result

Claims for the same collection are processed sequentially to ensure consistency. Claims for different collections can process in parallel.

State mutations from plugins are only committed if the overall claim succeeds. If any required plugin fails, no state changes are persisted.

## Example Use Cases

1. **Claim Success → Mint NFT** The user can immediately receive a minted NFT as a reward if the claim succeeds.
2. **Claim Success → Access to Gated Content** The user can immediately access gated content if the claim succeeds.

In very simple terms, a claim checks criteria. You decide what claim success means (rewards) and in what form you want to verify claim success (direct claim lookups, users get an NFT -> use NFT ownership as a credential, or so on).

## Completion Methods

**In-Site (Recommended):** Users claim directly on the BitBadges site, providing the necessary information to the plugins. Custom logic is handled by plugins — no code required for most use cases.

**API Auto-Completion:** Complete claims programmatically on behalf of users. Useful for backend-driven flows.

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, {
    _expectedVersion: '0',
    passwordInstanceId: { password: 'backend-secret' },
});
```

See [API Reference](api-reference.md) for full details on completing, simulating, and verifying claims.

## Plugins

Claims are composed of plugins — 8 core built-in plugins, 6 pre-built plugins maintained by BitBadges, and support for custom plugins (your own HTTP endpoints). See [Built-In Plugins](built-in-plugins.md) for schemas and details, or [Custom Plugins](custom-plugins.md) to build your own.

## Creating Claims

Claims are created and managed in the [developer portal](https://bitbadges.io/developer). Use the in-site claim builder and forms for no-code setup, or the API for programmatic creation.

## Do You Even Need Claims?

Claims are a convenience service — not a requirement. There is nothing special about them that you can't replicate yourself.

**You can self-host everything.** The on-chain Merkle challenge system is fully decentralized. You can generate your own Merkle trees, distribute leaves yourself, and let users submit proofs on-chain — no BitBadges claims involved. Any criteria-gating logic you want is just a script or a prompt away. Build your own system if you prefer full control.

**Claims are often overkill.** For many use cases, simpler alternatives exist:

- **Fully on-chain** — Use on-chain approval criteria directly (token ownership, dynamic store challenges, address checks). No off-chain component needed.
- **Snapshots** — Take a snapshot of eligible addresses and set approvals accordingly. No real-time criteria checking.
- **Direct API gating** — Check token ownership via the SDK/API in your own backend. No claims infrastructure needed.
- **Manual distribution** — Just send tokens directly to addresses. Not everything needs a claim flow.

**When claims make sense:**

- You want a **turnkey in-site experience** — claim builder UI, hosted forms, no frontend code
- You need **composable criteria** — combine multiple checks (codes + whitelist + token ownership) with AND/OR/NOT logic
- You want **managed state** — BitBadges tracks who claimed, usage limits, and code redemption
- You need **off-chain → on-chain gating** — claims handle Merkle proof generation and distribution seamlessly
- You want **plugin extensibility** — custom HTTP endpoints for any logic, with managed state and webhooks

Claims are one tool in the toolbox. Use them when the convenience outweighs the trust tradeoffs, and use simpler approaches when they don't.

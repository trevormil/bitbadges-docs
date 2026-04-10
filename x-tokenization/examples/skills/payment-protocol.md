# Payment Protocol

> Invoices, escrows, bounties, milestones, and multi-party agreements using coinTransfer-based approvals or IBC-backed smart token escrow

**Category:** Token Types

## Summary

Build invoices, milestones, bounties, escrow agreements, or any payment flow.

Two approaches:
- **Approach 1 (coinTransfer-based)**: Simple one-shot payments. Each approval = one invoice/milestone with coinTransfers.
  - Standards: ["ListView:Milestones"] or ["ListView:Invoice Requests"] or ["ListView:Bounties"]
  - Each approval: fromListId "Mint", coinTransfers for payment, overridesFromOutgoingApprovals: true
  - ListView incompatible with: Subscriptions, Smart Tokens, Custom 2FA, Liquidity Pools, Tradable NFTs
- **Approach 2 (Escrow)**: Funds held in IBC-backed smart token until conditions are met.
  - Standards: ["Smart Token"]
  - USDC/ATOM backed 1:1 into single token ID. Approvals control deposit, release, refund, dispute, timeout.
  - Typically 6-12+ approvals modeling the full lifecycle of a multi-party agreement.
  - All permissions permanently locked — no one can change rules after deployment.

Key design: each approval = one conditional branch. Not all get used — they define what CAN happen.
- Lock canUpdateCollectionApprovals for immutable terms
- Initiator pays gas; for mint-based, the payer initiates

## Instructions

## Payment Protocol

Build invoices, milestones, bounties, escrow agreements, or any payment flow.

### Which approach to use

**Default to Approach 2 (Smart Token Escrow)** unless the request is clearly a simple one-shot payment with no hold/release/refund logic. Smart token escrow is the superset — it can do everything coinTransfers can, plus escrow, conditional release, refunds, multi-party deposits, and dispute resolution. When in doubt, use Approach 2.

Use Approach 1 only for simple scenarios like: "create an invoice for 10 BADGE" or "milestone list where payer pays on completion" — where funds move immediately at transfer time with no hold period.

### Approach 1: coinTransfer-Based (Simple Payments)

Each approval is an invoice/milestone with coinTransfers. Uses the **ListView** display standard. Only for simple one-shot payments — no escrow, no hold-and-release, no refunds.

**Required:**
- Standards: ["ListView:Milestones"] or ["ListView:Invoice Requests"] or ["ListView:Bounties"]
- Each item = one collection-level approval with:
  - fromListId: "Mint" (for new tokens) or specific address (for transfers)
  - coinTransfers: payment amount and recipient
  - overridesFromOutgoingApprovals: true (if fromListId is "Mint")

**Invoice/Milestone Example:**
```json
{
  "collectionApprovals": [{
    "fromListId": "Mint",
    "toListId": "All",
    "initiatedByListId": "bb1payer...",
    "approvalId": "milestone-1",
    "tokenIds": [{ "start": "1", "end": "1" }],
    "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
    "approvalCriteria": {
      "coinTransfers": [{
        "to": "bb1payee...",
        "coins": [{ "denom": "ubadge", "amount": "10000000000" }],
        "overrideFromWithApproverAddress": false,
        "overrideToWithInitiator": false
      }],
      "overridesFromOutgoingApprovals": true,
      "maxNumTransfers": {
        "overallMaxNumTransfers": "1",
        "amountTrackerId": "milestone-1-tracker",
        "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" }
      }
    }
  }],
  "standards": ["ListView:Milestones"]
}
```

**ListView incompatibility**: ListView is incompatible with Subscriptions, Smart Tokens, Custom 2FA, Liquidity Pools, Tradable NFTs.

### Approach 2: Escrow (Smart Token)

Use a USDC/ATOM-backed smart token for hold-and-release escrow. Funds are deposited (backed) and released via approval-controlled transfers, then withdrawn (unbacked) for the underlying ICS20 coins.

**When to use escrow vs coinTransfers:**
- coinTransfers: One-shot payment at transfer time (simpler)
- Escrow: Funds held until conditions met, refundable, multi-party (more complex but trustless)

**Architecture: Three Phases**
```
PHASE 1 — DEPOSITS: Parties back ICS20 coins into the smart token (backing approvals)
PHASE 2 — RESOLUTION: Approvals control who can move tokens to whom, gated by conditions
PHASE 3 — WITHDRAWAL: Winners unback tokens to receive ICS20 coins
```

**Required structure:**
- Standards: ["Smart Token"]
- Invariants: cosmosCoinBackedPath with 1:1 conversion
- Alias path for display
- validTokenIds: [{ "start": "1", "end": "1" }] (single token, amount-capped approvals for logical buckets)
- ALL permissions permanently locked

**Escrow approval categories:**

1. **Backing approvals (deposits)** — each depositing party gets a separate backing approval with amount caps
```json
{
  "fromListId": "bb1backingAddress...",
  "toListId": "bb1posterAddress...",
  "initiatedByListId": "bb1posterAddress...",
  "approvalId": "poster-backing",
  "approvalCriteria": {
    "overridesFromOutgoingApprovals": true,
    "mustPrioritize": true,
    "allowBackedMinting": true,
    "maxNumTransfers": { "overallMaxNumTransfers": "1", "amountTrackerId": "poster-backing-tracker", "resetTimeIntervals": { "startTime": "0", "intervalLength": "0" } }
  }
}
```

2. **Release approvals (payment paths)** — control how funds move. Gate with:
   - Simple sign-off: initiatedByListId = poster address
   - Verifier-gated: votingChallenges with quorum threshold
   - mustOwnBadges: require deposit verification or credentials
   - approvalAmounts: cap how much each approval can move

3. **Timeout approvals (fallbacks)** — use transferTimes to gate when fallbacks activate
```json
{
  "approvalId": "timeout-release",
  "transferTimes": [{ "start": "DEADLINE_MS", "end": "18446744073709551615" }],
  "approvalCriteria": { "overridesFromOutgoingApprovals": true }
}
```
DEADLINE_MS = creation timestamp + timeout hours * 3600000

4. **Verifier fee approvals** — flat fee regardless of decision (neutral incentive). Two approvals: one gated by approve vote, one by deny vote.

5. **Deposit return/forfeit** — worker reclaims on success, poster takes on timeout. Natural mutex via balance depletion.

6. **Unbacking approval** — standard smart token unbacking. Anyone holding tokens can burn for ICS20 coins.

### Multi-Approval Lifecycle Design

Payment protocols typically have **6-12+ approvals** modeling every possible flow. Not all get used in a single transaction — they define the complete lifecycle of what CAN happen.

**Example: Freelancer escrow with 8 approvals**
1. `poster-deposit` — poster backs USDC into escrow
2. `worker-deposit` — worker deposits a bond (optional)
3. `release-on-completion` — poster releases funds to worker
4. `timeout-refund` — poster reclaims if worker ghosts (time-gated)
5. `dispute-resolution` — arbitrator releases funds (vote-gated)
6. `worker-bond-return` — worker reclaims bond on success
7. `worker-bond-forfeit` — poster takes bond on timeout
8. `penalty-fee` — arbitrator fee on dispute

Happy path uses #1, #3, #6. Dispute uses #1, #2, #5, #7, #8. Timeout uses #1, #4. The approvals define all possibilities.

**Common patterns:**
- **Pattern A — Simple Trust**: poster-backing + poster-release + timeout-release + unbacking (4 approvals)
- **Pattern B — Verified Third-Party**: + approve-release + deny-refund + verifier-fees (7+ approvals)
- **Pattern C — Mutual Deposit + Vote**: both parties deposit, 2-of-3 vote gates release (8+ approvals)
- **Pattern D — Milestones**: poster backs total budget, separate release approval per milestone (N+3 approvals)
- **Pattern E — Bounty**: poster backs bounty, award approval with toListId "All" + overallMaxNumTransfers "1" (4 approvals)

### How conditional branching works

All approvals are effectively **OR logic** — any approval can be satisfied as long as its criteria match. The chain doesn't enforce "if A then B" directly. To implement conditional flows, get creative with criteria composition:

- **Balance depletion as mutex**: approve-release and deny-refund target the same tokens. Once one fires, balance depletes and the other can't execute. Natural mutual exclusion.
- **mustOwnTokens for state gating**: mint soulbound tokens (from this or another collection) to represent state transitions, then require them via mustOwnTokens on downstream approvals. E.g., mint a "work-completed" badge, then the release approval requires holding it. Use collectionId "0" to self-reference this collection — the chain resolves it at runtime, avoiding the need to hardcode the collection ID.
- **transferTimes for temporal gating**: only allow certain approvals after a deadline passes.
- **votingChallenges for human decisions**: gate approvals behind explicit votes from designated parties.
- **Amount caps for partial flows**: use approvalAmounts to limit how much each branch can move, preventing over-claiming.

The primitives (mustOwnTokens, transferTimes, votingChallenges, amount caps, balance depletion) combine to model complex conditional logic even though each approval is independently satisfiable.

### Decision Tree

1. **Default to Smart Token Escrow (Approach 2)** unless clearly a simple one-shot payment. Escrow handles everything — payments, holds, refunds, disputes, multi-party flows. Only use coinTransfers (Approach 1) for trivially simple invoices with no hold period.
2. **Permission locking**: For agreements, lock `canUpdateCollectionApprovals` so terms are immutable.
3. **Who pays gas?**: The initiator pays gas. For mint-based, the payer initiates.
4. **Multiple currencies**: A single collection can accept different IBC denoms in different approvals.
5. **Refunds**: For coinTransfer-based, refunds require a separate approval. For escrow, use timeout-refund approvals.
6. **Timeouts**: Every escrow path MUST have a timeout fallback — without them, funds can be locked forever.

## Common Mistakes

- DON'T use multiple token IDs for different "buckets" in escrow — use a single token ID with amount-capped approvals instead.
- DON'T forget timeout fallbacks on every path — funds can be locked forever if a party ghosts.
- DON'T make verifier fee conditional on outcome — use flat fee with two vote-gated fee approvals for neutral incentives.
- DON'T forget to lock ALL permissions for escrow — any unlocked permission lets someone change the rules.
- DON'T use the same amountTrackerId across multiple approvals unless you want them to share a counter.
- DON'T forget mustOwnBadges for deposit verification — this is how you on-chain gate releases on deposits.

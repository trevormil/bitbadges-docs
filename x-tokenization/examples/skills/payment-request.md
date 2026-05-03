# PaymentRequest

> Agent-initiated payment request with no escrow. The agent (or any address) creates a collection requesting payment from a targeted human payer. The payer approves AND pays from their own wallet in a single action. Inverse of Bounty.

**Category:** Token Types

## Summary

Required standards: ["PaymentRequest"]

- 1 token ID (vehicle for approval engine — minted directly to burn)
- 2 collection-level approvals: pay, deny
- Each approval: Mint → burn 1x token ID 1
- Pay approval triggers a coinTransfer FROM the payer's wallet TO the recipient
- NO mintEscrowCoinsToTransfer — payment debits the payer's wallet at execution time
- Approval gating via initiatedByListId scoped to the payer (no votingChallenges)
- Fixed payment amount, no amount scaling
- Both approvals maxNumTransfers = 1 (one-shot)
- All permissions frozen after creation
- Expiration is implicit — both approvals share `transferTimes: [{ start: 1, end: expirationTimestamp }]`; once that window closes neither can fire (no separate expire approval, since there's no escrow to refund)

## Mental Model

PaymentRequest is the **inverse of Bounty**: instead of an escrow-based reward where the submitter pre-funds and the verifier votes, this is an agent-initiated payment request where the targeted human payer approves AND pays from their own wallet in one action.

Two parties:
- **Requester** (agent or merchant): Creates the collection, specifies payer + amount + recipient
- **Payer** (human): Sees the request, decides to approve+pay or deny

There's NO escrow. The payment doesn't move until the payer signs the approval. Funds debit directly from the payer's wallet at execution because the pay approval uses `overrideFromWithApproverAddress: false` — the chain default routes the coinTransfer's "from" to the initiator (the payer, scoped via `initiatedByListId`).

This is the on-chain equivalent of [Stripe Link](https://github.com/stripe/link-cli)'s spend-request flow: the agent presents a payable artifact with rationale, the human approves, the credential settles. Mirror the rationale-bound, single-use, expiry-gated pattern — but with chain rails instead of card rails.

## Token Structure

- Token ID 1 = PaymentRequest token (vehicle for approval engine)
- validTokenIds: [{ start: "1", end: "1" }]
- No alias path needed (1-of-1 receipt-style token)

## 2 Required Approvals

Both approvals share: Mint → burn address, 1x token ID 1, maxNumTransfers = 1, overridesFromOutgoingApprovals=true, overridesToIncomingApprovals=true, transferTimes `[{ start: "1", end: expirationTimestamp }]`. **NO votingChallenges** — gating is via initiatedByListId, not voting.

### 1. Pay (payment-request-pay-*)

Payer approves → mint-to-burn → coins move from payer to recipient.

Key fields:
- `fromListId`: "Mint"
- `toListId`: burn address (`bb1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqs7gvmv`)
- `initiatedByListId`: payer's bb1... address (NOT "All" — gates approval to just the payer)
- `coinTransfers`: `[{ to: recipientAddress, overrideFromWithApproverAddress: false, overrideToWithInitiator: false, coins: [{ denom, amount }] }]`
  - **CRITICAL**: `overrideFromWithApproverAddress` MUST be `false`. The chain default routes the coinTransfer's "from" to the initiator (the payer). Setting `true` would attempt to debit a non-existent escrow → tx fails.
- `transferTimes`: `[{ start: "1", end: expirationTimestamp }]`
- `maxNumTransfers.overallMaxNumTransfers`: "1"

### 2. Deny (payment-request-deny-*)

Payer rejects → mint-to-burn → no coin transfer. Records denial state for indexers/UIs.

Same shape as Pay but:
- NO `coinTransfers` (or empty array)
- Same `initiatedByListId` (payer)
- Same `transferTimes` (concurrent with pay)

### Why no expire branch?

Bounty has an expire approval to refund escrowed funds back to the submitter once the deadline passes. PaymentRequest has no escrow — if the payer doesn't act before `expirationTimestamp`, both approvals' time windows close and the request is simply un-actionable. There's nothing to refund and no terminal state to record on-chain. UIs detect "expired" by comparing the current time to `transferTimes[0].end`. Validators reject collections with more than 2 approvals.

## Settlement Flow

1. Agent (or merchant) creates the collection — **NO escrow funded**.
2. Payer sees the request in their dashboard / wallet (rationale, line items, amount).
3. Payer either:
   - **Approves+pays**: signs `MsgTransferTokens` from `Mint` → burn (1x token ID 1) with `prioritizedApprovals` targeting the pay approval. Coins debit from their wallet to the recipient automatically.
   - **Denies**: signs `MsgTransferTokens` targeting the deny approval. No coins move.
4. If the payer does neither before `expirationTimestamp`, both approvals become un-fireable and the request is implicitly expired. No on-chain action is needed to "finalize" expiration.

## Key Differences from Bounty

- **NO `mintEscrowCoinsToTransfer`** at the collection level
- **Pay approval uses `overrideFromWithApproverAddress: false`** (Bounty uses `true`)
- **No `votingChallenges`** — gating is via `initiatedByListId` scoped to payer
- **Deny has no `coinTransfers`** — no funds need to be returned (no escrow to refund)
- **No expire approval** — expiration is implicit via the shared `transferTimes[0].end`. Bounty needs an expire branch to refund escrow; we don't have escrow.
- **2 approvals** instead of Bounty's 3
- Same mint-to-burn vehicle, same frozen permissions

## Creation Flow (CLI)

```bash
bitbadges-cli build payment-request \
  --amount 10 \
  --denom USDC \
  --payer bb1payer... \
  --recipient bb1agent... \
  --expiration 30d \
  --name "Service charge" \
  --context "Agent X is requesting payment for completed deliverable Y on behalf of org Z under approved budget envelope of \$100/month."
```

The `--context` flag populates the collection metadata description with the rationale shown to the payer at approval time. Aim for ≥100 chars (Stripe Link's bar) to force agents to justify the spend.

## Creation Flow (Builder Tool Calls)

1. Use per-field tools to initialize the collection
2. `set_valid_token_ids` — set `[{ start: "1", end: "1" }]`
3. `set_standards` — set `["PaymentRequest"]`
4. `set_invariants` — set `{ noCustomOwnershipTimes: true, disablePoolCreation: true, noForcefulPostMintTransfers: true }`
5. **DO NOT** call `set_mint_escrow_coins` — there's no escrow
6. `add_preset_approval` x2 — pay, deny (or `add_approval` for raw)
7. `set_permissions` — freeze all permissions
8. `set_collection_metadata` — name + the rationale (≥100 chars recommended)
9. `set_token_metadata` — token 1 metadata
10. `validate_transaction` — verify structure (verifyPaymentRequest enforces the no-escrow invariants)
11. `simulate_transaction` — dry run

## Permissions

All permissions MUST be frozen (same set as Bounty).

## Common Mistakes

- **DON'T set `overrideFromWithApproverAddress: true`** on the pay approval — that's the Bounty pattern. PaymentRequest needs `false` so the chain debits the payer (initiator), not a non-existent escrow.
- **DON'T add `votingChallenges`** — PaymentRequest gating is via `initiatedByListId`, not voting. Voting is a Bounty construct.
- **DON'T fund `mintEscrowCoinsToTransfer`** — there is no escrow. The payer pays at execution time.
- **DON'T set `initiatedByListId` to "All" on pay/deny** — that would let anyone approve. Lock to the specific payer's address.
- **DON'T forget the rationale** in collection metadata description — it's what the human reads to decide. Aim for ≥100 chars (Stripe Link's bar) to force agents to justify the spend.
- **DON'T use `overrideToWithInitiator`** on the coinTransfer — the recipient is hardcoded.

## Relationship to the Invoices Standard

The existing [`Invoices` standard](./payment-protocol.md) validates a single payer-as-initiator approval — useful as a building block, but it has no deny branch and no targeted-payer scoping. PaymentRequest is a more constrained, agent-payments-specific subset: same payment direction (initiator → address), but with an explicit pay+deny pair so the payer's "no" is captured on-chain rather than being indistinguishable from "hasn't acted yet".

Consumers that want any payer-initiated payment can match `Invoices`; consumers that want the agent-payments artifact specifically should match `PaymentRequest`.

## Why this exists

[Stripe shipped link-cli](https://github.com/stripe/link-cli) in April 2026: an agent CLI for one-shot, human-approved payment credentials backed by Stripe Link's wallet. Agents create a "spend request" with merchant info + ≥100-char rationale + amount, the user approves on their phone, the agent gets a one-time virtual card to pay the merchant.

PaymentRequest is the on-chain equivalent. The chain rails offer things Stripe Link cannot:

1. **Programmable allowances**: a payer can grant the agent a BitBadges approval ("≤50 USDC/week, ≤10 USDC/tx, expires 30d") so in-envelope requests auto-approve and out-of-envelope requests wake the human. Stripe needs a whole policy engine for this; we get it from the existing approval primitive.
2. **Receipts as badges**: the paid request optionally mints a "purchased X from Y" badge into the payer's collection. Audit trail is automatic.
3. **MPP / HTTP 402 interop**: speak the same wire format as [Machine Payments Protocol](https://mpp.dev) and any MPP-compatible client (including Stripe's link-cli with `credential_type: shared_payment_token`) can pay BitBadges merchants, and BitBadges-native agents can pay anyone speaking MPP.

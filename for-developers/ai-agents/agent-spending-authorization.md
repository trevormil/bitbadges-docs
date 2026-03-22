# Protocol-Level Spending Authorization for AI Agents

Agentic payments are only as trustworthy as the rules that govern them. If your AI agent wallet operates without protocol-level spending limits enforced on-chain, you are relying entirely on a platform promise — and platform promises break. This guide explains how BitBadges lets you encode agent spending authorization directly into the blockchain, so the rules are not a setting in a dashboard but a constraint the protocol itself enforces.

## Why App-Layer Limits Are Not Enough

Several platforms (Crossmint, Coinbase Agentic Wallets, and others) offer spending limit controls at the application layer. You set a cap in their UI, and their servers enforce it. This sounds convenient, but it has a fundamental weakness: the platform controls the rule.

If the company changes its policy, the cap changes. If their system is hacked, the cap can be bypassed. If their API is social-engineered, the cap disappears. There is no on-chain audit trail of what was authorized, no cryptographic proof that the limit existed, and no way for an external party to verify it independently.

App-layer limits are a promise, not a constraint. For low-stakes automations that may be acceptable. For enterprise deployments, regulated assets, or high-value agent wallets, it is not.

## How Protocol-Level Authorization Works

With BitBadges, authorization rules live on-chain inside the collection's approval configuration — not in a database. A human controller sets up a delegate wallet with a defined spending envelope, and the blockchain enforces that envelope at the transaction level. The agent physically cannot move tokens outside those parameters, not because a server checked a flag, but because the protocol rejects the transaction.

The key building blocks are:

- **Delegate wallet** — The agent holds a separate keypair with no permissions except what the approval explicitly grants. The controller's main wallet remains untouched.
- **Daily spending caps that reset automatically** — Approvals can track cumulative amounts transferred over a rolling window (hourly, daily, monthly). Once the cap is hit, the chain rejects further transfers until the window resets. No server needed.
- **Valid time windows** — Approvals can specify the exact time range during which transfers are permitted. Outside that window, the protocol refuses the transaction outright.
- **Token and recipient allowlists** — Approvals can be scoped to specific token IDs and destination addresses. The agent cannot move tokens that are not on the list, or send to addresses not pre-approved.
- **Instant revocation** — The controller can invalidate the approval in a single transaction. From that block forward, the agent's wallet is inert.

The approval configuration encodes all of this directly. The relevant fields are the tallied transfer tracking with time-based resets for daily caps, time-range constraints on when transfers are valid, and address-based allowlists for tokens and recipients.

## Four Canonical Agent Authorization Patterns

### AI Research Agent

A research agent needs to pay for API calls, data queries, and computation credits throughout the day, but the controller wants a hard ceiling to prevent runaway spending. The approval is configured with a daily spending cap — say, 100 credits per day — using a tallied transfer tracker with a 24-hour reset window. When the agent hits 100 credits, every subsequent transaction is rejected until midnight resets the counter. The valid time window can be set to expire at end of day, forcing a daily re-authorization from the human controller. This pattern ensures the agent can never accumulate multi-day debt even if it runs continuously.

### Employee Expense Wallet

An employee receives a delegated wallet for business expenses. The approval carries a monthly budget with an expiry date matching the end of the pay period. When the month ends, the approval expires automatically — the employee's wallet stops working without any action from HR. Each month, the controller broadcasts one transaction to write the new time window and budget. The old approval is gone; the new one is live. This pattern replaces manual revocation workflows with a calendar-driven, trustless cycle.

### DAO Sub-Committee

A sub-committee is allocated a 90-day sprint budget of up to 10,000 tokens to deploy toward grants and partnerships. The approval is configured with a cumulative cap (the entire 90-day total cannot exceed 10,000 tokens) and a hard expiry at the sprint end date. Trustees hold a separate key with manager-level permissions that can override or reduce the cap at any time. The sub-committee can spend freely within the envelope but cannot exceed it — even if all members vote to do so. The protocol ignores the vote; only the approval configuration matters.

### Contractor Wallet

A contractor is granted spending access for the duration of an engagement. Rather than time-windowing the approval, access is gated through a dynamic store challenge — a condition that checks an on-chain flag controlled by the client. While the flag is set, the contractor's wallet works. The moment the client broadcasts a single transaction to flip the flag, every subsequent transfer from the contractor's wallet is rejected. There is no waiting period, no support ticket, no API call to a third party. One transaction, instant revocation, provable on-chain.

## Instant Revocation and Audit Trail

Revocation in the BitBadges model is a first-class operation, not an afterthought. The controller broadcasts one transaction to remove or modify the approval. From the next block forward, the agent's wallet cannot move tokens under that approval. No delay, no batch processing window, no platform intermediary.

Every transfer the agent makes is recorded on-chain with a timestamp, the approval ID it was authorized under, the amounts moved, and the recipient addresses. This is not a log file you maintain — it is the canonical blockchain record. An auditor, regulator, or enterprise security team can independently verify the entire spending history without requesting data from any platform.

For enterprise buyers evaluating agentic payment infrastructure, this is the compliance deliverable: a provable, tamper-resistant record of what was authorized, what was spent, when, and under whose delegation — all without trusting a vendor's self-reported data.

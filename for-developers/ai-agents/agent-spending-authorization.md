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

## Instant Revocation and Audit Trail

Revocation in the BitBadges model is a first-class operation, not an afterthought. The controller broadcasts one transaction to remove or modify the approval. From the next block forward, the agent's wallet cannot move tokens under that approval. No delay, no batch processing window, no platform intermediary.

Every transfer the agent makes is recorded on-chain with a timestamp, the approval ID it was authorized under, the amounts moved, and the recipient addresses. This is not a log file you maintain — it is the canonical blockchain record. An auditor, regulator, or enterprise security team can independently verify the entire spending history without requesting data from any platform.

For enterprise buyers evaluating agentic payment infrastructure, this is the compliance deliverable: a provable, tamper-resistant record of what was authorized, what was spent, when, and under whose delegation — all without trusting a vendor's self-reported data.

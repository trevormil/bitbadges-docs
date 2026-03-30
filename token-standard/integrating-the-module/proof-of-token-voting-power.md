# Proof-of-Token Voting Power (x/pot)

What if your chain could have all the economic security of Proof of Stake — staking, delegation, slashing — but only credentialed validators could produce blocks?

`x/pot` is a module that gates validator voting power on token ownership. It works with both **PoS** (x/staking) and **PoA** chains: if a validator holds the required credential token, they keep their power. If they don't, they're compliance-jailed and their power drops to zero. One binary check, zero changes to your existing consensus economics.

The credential token is a regular `x/tokenization` token, so you get every primitive for free: time-dependent expiry, multi-sig issuance, KYC gates, instant revocation, on-chain audit trail. No custom governance proposals, no manual validator management.

## Why Use x/pot?

**PoA** gives you permissioned validators but no slashing — there's no economic stake at risk. **PoS** gives you slashing but anyone who stakes enough can validate. `x/pot` lets you add a programmable compliance gate on top of *either* model:

- **On a PoS chain (x/staking):** Validators must hold a credential to have voting power. Lose the credential and x/pot jails them. Regain it and they're automatically unjailed. All staking economics (delegation, slashing, rewards) work normally.
- **On a PoA chain:** Validators must hold a credential to keep their power. Lose it and x/pot sets their power to 0. Regain it and power is restored. The PoA authority structure stays the same — x/pot just adds a token-based compliance layer.

In both cases, who can validate is governed by token rules — not a single admin. Credentials expire, require multi-sig approval, gate on KYC status, and leave a full on-chain audit trail.

## How It Works

The formula is a binary gate:

```
voting_power = normal_power * (1 if credential_balance >= minBalance, else 0)
```

| Scenario | Voting Power |
|---|---|
| 1M staked + holds credential | 1M (normal PoS) |
| 1M staked + no credential | 0 (compliance-jailed) |
| Jailed for slashing (any credential status) | 0 (x/staking respected) |

Everything else stays the same: `MsgCreateValidator`, delegation, undelegation, slashing, jailing, evidence, rewards — all handled by existing modules. `x/pot` doesn't replace any of it.

### Architecture: EndBlocker with Jail/Unjail

Every block, the EndBlocker iterates all active validators and checks their credential token balance at the current block time. This catches both transfer-based changes and time-dependent credential expiry.

- **Lost credential:** Validator is compliance-jailed (power set to 0). x/pot tracks this separately from slashing jails so it knows the difference.
- **Regained credential:** Validator is auto-unjailed — but only if they aren't also jailed for slashing or tombstoned.
- **Safety:** x/pot will never disable ALL validators. If removing non-compliant validators would leave zero active validators, it skips disabling and logs a critical warning instead of halting the chain.

All state changes run inside a `CacheContext` for atomicity. If anything fails, the entire block's changes roll back cleanly.

**For PoA chains:** Instead of jailing, the PoA adapter saves the validator's current power, sets it to 0 on disable, and restores the saved power on re-enable.

## The Credential Token

The credential is a standard `x/tokenization` token. Every primitive you already know applies automatically:

| Primitive | What It Does for Validators |
|---|---|
| **Time-dependent balances** | Credential expires annually — validator must re-certify or loses power |
| **mustOwnTokens** | Must hold a KYC token + jurisdiction license to receive the credential |
| **Multi-sig issuance** | Voting challenge on mint approval — no single admin can credential a validator |
| **Non-transferable** | Credentials can't be sold or traded without issuer approval |
| **Freezable / revocable** | Regulator can instantly revoke a validator's credential |
| **Max supply** | Caps the maximum number of credentialed validators |
| **Dynamic address stores** | Approved jurisdictions list — credential only issuable to addresses in approved regions |
| **Off-chain criteria** | Gate credential issuance on an off-chain KYC provider |

No extra code. These are all features of the token standard that the credential inherits automatically.

## Module Parameters

```protobuf
message Params {
  uint64 credential_collection_id = 1;  // x/tokenization collection ID (0 = disabled)
  uint64 credential_token_id = 2;       // token ID within the collection
  uint64 min_credential_balance = 3;    // minimum balance to pass the gate (default: 1)
  string mode = 4;                      // "staked_multiplier" (default)
}
```

The module is disabled when `credential_collection_id` is 0 — the EndBlocker becomes a no-op.

Currently, `staked_multiplier` is the only supported mode. This is the binary compliance gate: full PoS/PoA power when the credential is held, zero power when it isn't.

## Validator Lifecycle

1. **Join:** Validator creates via `MsgCreateValidator` (normal x/staking) or is added to the PoA set. Without a credential, power = 0.
2. **Credentialed:** Receives credential token. Next block, x/pot detects the credential and auto-unjails (PoS) or restores power (PoA).
3. **Active:** Produces blocks with normal power. Everything works as usual.
4. **Renewal:** Credential has a time-dependent balance. When it expires, the EndBlocker detects the zero balance and compliance-jails the validator. Must re-certify to regain power.
5. **Revocation:** Authority burns or freezes credential. Compliance-jailed next block.
6. **Slashing jail:** Normal x/staking jailing. Power = 0 regardless of credential. x/pot won't auto-unjail a validator that's also jailed for slashing.
7. **Exit:** Validator unbonds normally. Credential can be burned or returned.

## PoS vs PoA Integration

x/pot uses a `ValidatorSetKeeper` abstraction with two adapters:

| | **PoS (x/staking)** | **PoA** |
|---|---|---|
| **Disable** | Jail the validator | Save power, set to 0 |
| **Enable** | Unjail the validator | Restore saved power |
| **Safety check** | Respects tombstone/slashing jail | Always safe to enable |
| **Slashing** | Yes (staked tokens at risk) | No |

Wire the appropriate adapter in your `app.go`. The x/pot EndBlocker logic is identical for both — only the adapter changes.

## Use Cases

### Regulated RWA Chains
Regulators require known, KYC'd validators. Credential = KYC verification + regulatory license. Expires annually, requires multi-sig committee approval for renewal. Chain gets PoS security without anonymous validators.

### CBDC Infrastructure
Government-issued chain where validators must be licensed financial institutions. Credential issued by central bank multi-sig. Full PoS economics for security, compliance gate for participation.

### Consortium / Permissioned Networks
Members of a consortium each hold a credential token. Same trust model as PoA but with programmable membership: credentials expire, multi-sig committee issues new credentials, KYC-gated entry, full on-chain audit trail. No single admin controls the validator set.

### Cross-Chain Validator Compliance
Validators on multiple Cosmos chains can share a credential. Hold one KYC credential token and qualify across every chain that uses `x/pot` with the same credential collection.

## Relationship to Ante Handler Token Gates

The [Ante Handler Token Gates](ante-handler-token-gates.md) page shows how to gate **transactions** on token ownership — blocking specific message types unless the sender holds a credential. `x/pot` operates at a different level: it gates **consensus participation** on token ownership.

You can use both together. Ante handlers gate what users can *do*. `x/pot` gates who can *validate*. A fully compliant chain might use ante handlers for KYC-gated transfers **and** `x/pot` for credentialed validators.

## Getting Started

To integrate `x/pot` into your chain:

1. Import the `x/pot` module in your `app.go`
2. Wire either the **staking adapter** (for PoS chains) or the **PoA adapter** (for PoA chains) as the `ValidatorSetKeeper`
3. Create a credential token collection using `x/tokenization`
4. Configure the module params with your collection ID and token ID
5. Set up credential issuance rules on the token (time-dependent expiry, multi-sig approval, KYC gates — whatever your compliance model requires)
6. Issue credential tokens to approved validators

The module handles the rest. Validators without credentials are automatically compliance-jailed. Validators who regain credentials are automatically restored. No manual intervention required.

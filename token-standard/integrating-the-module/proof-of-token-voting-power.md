# Proof-of-Token Voting Power (x/pot)

What if your chain could have all the economic security of Proof of Stake — staking, delegation, slashing — but only credentialed validators could produce blocks?

`x/pot` is a module that gates validator voting power on token ownership. It sits on top of `x/staking` and multiplies: if a validator holds the required credential token, their stake-based power applies normally. If they don't, their power drops to zero. One binary check, zero changes to staking economics.

The credential token is a regular `x/tokenization` token, so you get every primitive for free: time-dependent expiry, multi-sig issuance, KYC gates, instant revocation, on-chain audit trail. No custom governance proposals, no manual validator management, no PoA trade-offs.

## Why Not Just PoA?

If your chain has a central authority that manually manages a validator list, you don't need this — just use PoA. `x/pot` is for chains that want **both**:

- **PoS economic security** — validators have real stake at risk. Misbehave and you get slashed. PoA doesn't have this.
- **Programmable compliance** — who can validate is governed by token rules, not a single admin. Credentials expire, require multi-sig approval, gate on KYC status, and leave a full audit trail.

PoA gives you permissioned validators but no slashing. PoS gives you slashing but no permissioning. `x/pot` gives you both.

## How It Works

The formula is simple:

```
voting_power = staked_amount * (1 if credential_balance > 0, else 0)
```

| Scenario | Voting Power |
|---|---|
| 1M staked + holds credential | 1M (normal PoS) |
| 1M staked + no credential | 0 (compliance gate) |
| Jailed (any credential status) | 0 (x/staking respected) |

Everything else stays the same: `MsgCreateValidator`, delegation, undelegation, slashing, jailing, evidence, rewards — all handled by existing `x/staking` / `x/slashing` / `x/evidence`. `x/pot` doesn't replace any of it.

### Architecture

When a credential token is transferred, `x/pot` queues the affected addresses. At the end of the block (after `x/staking`'s EndBlocker), it checks whether each queued validator still holds the credential. If not, it emits a `ValidatorUpdate` that sets their power to zero.

The queue lives in a transient store (auto-cleared each block, deduplicated). If no credential tokens moved this block, the EndBlocker is a zero-cost no-op.

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

## Consensus Modes

`x/pot` supports three modes, configured via module params:

### `staked_multiplier` (default) — Compliant PoS

```
power = staked_amount * (1 if credential_balance >= minBalance, else 0)
```

Full PoS economics. The credential is a binary compliance gate on top of normal staking. Validators earn rewards proportional to stake, get slashed for misbehavior, and must hold a credential to have any power at all.

**For:** Regulated RWA chains, institutional Cosmos chains, CBDC infrastructure — anywhere you need PoS security with a compliant validator set.

### `equal` — Democratic Consensus with Slashing

```
power = 1 if credential_balance >= minBalance, else 0
```

All credentialed validators have equal voting power regardless of stake. Validators still self-delegate (the staked tokens serve as a security deposit for slashing), but power comes from the credential, not capital.

**For:** Consortium chains where all members should have equal say. 5 banks running a shared ledger each hold a credential token. Same trust model as PoA but with slashing, programmable membership, and an audit trail.

### `credential_weighted` — Proportional Power from Token Balance

```
power = credential_balance if credential_balance >= minBalance, else 0
```

Voting power scales with the credential token balance, decoupled from staked amount. A member holding 10 credential tokens has twice the power of a member holding 5, regardless of how much each has staked.

**For:** Consortiums that want weighted power based on membership tier, contribution level, or reputation — not capital.

## Validator Lifecycle

1. **Join:** Validator creates via `MsgCreateValidator` (normal `x/staking`). Without a credential, power = 0.
2. **Credentialed:** Receives credential token. Next block, `x/staking` power takes effect.
3. **Active:** Produces blocks proportional to staked amount (or equal/weighted, depending on mode).
4. **Renewal:** Credential has a time-dependent balance. When it expires, power drops to 0. Validator must re-certify.
5. **Revocation:** Authority burns or freezes credential. Power drops to 0 next block.
6. **Jailing:** Normal `x/staking` jailing. Power = 0 regardless of credential.
7. **Exit:** Validator unbonds normally. Credential can be burned or returned.

## Module Parameters

```go
type Params struct {
    CredentialCollectionId uint   // x/tokenization collection ID
    CredentialTokenId      uint   // token ID within the collection
    MinCredentialBalance   uint   // minimum balance to pass the gate (default: 1)
    Mode                   string // "staked_multiplier" | "equal" | "credential_weighted"
}
```

## Use Cases

### Regulated RWA Chains
Regulators require known, KYC'd validators. Credential = KYC verification + regulatory license. Expires annually, requires multi-sig committee approval for renewal. Chain gets PoS security without anonymous validators.

### CBDC Infrastructure
Government-issued chain where validators must be licensed financial institutions. Credential issued by central bank multi-sig. Full PoS economics for security, compliance gate for participation.

### Cross-Chain Validator Compliance
Validators on multiple Cosmos chains can share a credential. Hold one KYC credential token and qualify across every chain that uses `x/pot` with the same credential collection.

## Relationship to Ante Handler Token Gates

The [Ante Handler Token Gates](ante-handler-token-gates.md) page shows how to gate **transactions** on token ownership — blocking specific message types unless the sender holds a credential. `x/pot` operates at a different level: it gates **consensus participation** on token ownership.

You can use both together. Ante handlers gate what users can *do*. `x/pot` gates who can *validate*. A fully compliant chain might use ante handlers for KYC-gated transfers **and** `x/pot` for credentialed validators.

## Getting Started

To integrate `x/pot` into your chain:

1. Import the `x/pot` module in your `app.go`
2. Create a credential token collection using `x/tokenization`
3. Configure the module params with your collection ID, token ID, and desired mode
4. Set up credential issuance rules on the token (time-dependent expiry, multi-sig approval, KYC gates — whatever your compliance model requires)
5. Issue credential tokens to approved validators

The module handles the rest. No custom EndBlocker logic, no manual validator management, no governance proposals for routine operations.

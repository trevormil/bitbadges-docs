# Proof-of-Token Voting Power (x/pot)

{% hint style="warning" %}
**Experimental** — This module is under active development and not yet production-ready. If you're interested in using credential-gated consensus for your chain, [reach out to us](https://bitbadges.io/contact) to learn more or discuss your use case.
{% endhint %}

What if your chain could have all the economic security of Proof of Stake — staking, delegation, slashing — but only credentialed validators could produce blocks?

That's the idea behind x/pot. It adds a compliance gate on top of your existing consensus model — whether that's PoS or PoA. If a validator holds the required credential token, they keep their power. If they don't, their power drops to zero. One binary check, zero changes to your existing consensus economics.

The credential token is a regular token on the BitBadges token standard, so you get every primitive for free: time-dependent expiry, multi-sig issuance, KYC gates, instant revocation, on-chain audit trail. No custom governance proposals, no manual validator management.

## The Problem

**PoA** gives you permissioned validators but no slashing — there's no economic stake at risk. **PoS** gives you slashing but anyone who stakes enough can validate. Neither model alone gives you compliant consensus with real economic security.

x/pot bridges the gap by adding a **programmable credential layer** on top of either model:

- **PoS chains:** Validators must hold a credential to have voting power. Lose the credential and they're automatically disabled. Regain it and they're restored. All staking economics (delegation, slashing, rewards) continue working normally.
- **PoA chains:** Validators must hold a credential to keep their power. The existing authority structure stays the same — x/pot just adds a token-based compliance layer on top.

In both cases, who can validate is governed by token rules — not a single admin. Credentials expire, require multi-sig approval, gate on KYC status, and leave a full on-chain audit trail.

## How It Works

The core idea is a binary gate on voting power:

```
voting_power = normal_power * (1 if credential_balance >= minBalance, else 0)
```

| Scenario | Voting Power |
|---|---|
| 1M staked + holds credential | 1M (normal PoS) |
| 1M staked + no credential | 0 (disabled) |
| Jailed for slashing (any credential status) | 0 (existing rules respected) |

Everything else stays the same — delegation, undelegation, slashing, jailing, evidence, rewards. x/pot doesn't replace any existing module; it layers on top.

Each block, the module checks every active validator's credential balance. This catches both token transfers and time-dependent credential expiry. Validators who lose their credential are disabled; validators who regain it are automatically restored (as long as they aren't also jailed for slashing). As a safety measure, the module will never disable *all* validators — it would rather leave non-compliant validators running than halt the chain.

## The Credential Token

The credential is a standard BitBadges token, so every primitive applies automatically — time-dependent expiry, multi-sig issuance, non-transferability, freezing/revocation, max supply caps, KYC gates, off-chain criteria, and more. No extra code. A credential that expires annually, requires multi-sig approval to issue, and can be instantly revoked by a regulator is just a token with the right configuration.

## Validator Lifecycle

1. **Join:** Validator joins the network normally. Without a credential, they have no voting power.
2. **Credentialed:** Receives a credential token. Next block, their power is restored.
3. **Active:** Produces blocks normally. Everything works as usual.
4. **Renewal:** Credential has a time-dependent balance. When it expires, power drops to 0. Validator must re-certify.
5. **Revocation:** Authority burns or freezes the credential. Power drops to 0 next block.
6. **Exit:** Validator leaves the network normally. Credential can be burned or returned.

## Works with PoS and PoA

x/pot is designed to work with both consensus models:

| | **PoS** | **PoA** |
|---|---|---|
| **Credential lost** | Validator jailed | Power set to 0 |
| **Credential regained** | Validator unjailed | Power restored |
| **Slashing** | Yes (staked tokens at risk) | N/A |

The module logic is identical for both — only the underlying consensus integration differs.

## Use Cases

### Regulated RWA Chains
Regulators require known, KYC'd validators. Credential = KYC verification + regulatory license. Expires annually, requires multi-sig committee approval for renewal. Chain gets PoS security without anonymous validators.

### CBDC Infrastructure
Government-issued chain where validators must be licensed financial institutions. Credential issued by central bank multi-sig. Full PoS economics for security, compliance gate for participation.

### Consortium / Permissioned Networks
Members of a consortium each hold a credential token. Same trust model as PoA but with programmable membership: credentials expire automatically, multi-sig committee issues new credentials, KYC-gated entry, full on-chain audit trail. No single admin controls the validator set.

### Cross-Chain Validator Compliance
Validators on multiple Cosmos chains can share a credential. Hold one KYC credential token and qualify across every chain that recognizes the same credential collection.

## Relationship to Ante Handler Token Gates

The [Ante Handler Token Gates](ante-handler-token-gates.md) page shows how to gate **transactions** on token ownership — blocking specific message types unless the sender holds a credential. x/pot operates at a different level: it gates **consensus participation** on token ownership.

You can use both together. Ante handlers gate what users can *do*. x/pot gates who can *validate*. A fully compliant chain might use ante handlers for KYC-gated transfers **and** x/pot for credentialed validators.

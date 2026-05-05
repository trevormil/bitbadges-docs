# The Compliance Zone Architecture

Most chains dealing with regulated assets on Cosmos eventually face the same question: where does compliance live? In the token? In the chain's bank module? At the bridge? In every smart contract that touches the asset?

BitBadges answers: **at the boundary**.

The compliance-zone architecture splits the chain into two zones:

- **Open zone** — vanilla Cosmos behavior. `sdk.Coin` denoms (native gas token, IBC-arrived stablecoins, bridge wrappers), public bank transfers, standard staking, vanilla IBC. No compliance gates.
- **Compliance zone** — assets managed by `x/tokenization`. Transfers, ownership, and operations are governed by the approval engine. Sanctions, KYC, jurisdiction, holding periods, multi-sig escrow, rate limits — whatever the regime requires.

The zones are connected by one explicit boundary: the **wrap/unwrap operation**, implemented as `CosmosCoinBackedPath` and `CosmosCoinWrapperPath` in `x/tokenization`. Funds flow into the compliance zone by being wrapped (`sdk.Coin` → tokenization token); they exit by being unwrapped. The approval engine fires at the wrap step, gating who can enter.

## Why two zones

The alternative — gating compliance at every layer of the chain (bank, IBC, staking, gov, every contract) — has architectural problems:

- **Surface area.** Compliance code spreads across modules; auditors have to verify each integration.
- **Ecosystem compatibility.** If your bank module rejects vanilla transfers, IBC counterparty chains can't predict behavior; wallets and indexers break.
- **Update friction.** Changing a compliance rule means modifying multiple call sites, possibly across forked SDK modules.
- **All-or-nothing.** You can't have *some* assets compliant and *some* permissionless on the same chain.

The compliance-zone model avoids all four:

- **One audit surface.** The boundary lives entirely inside `x/tokenization`. No SDK fork, no scattered ante decorators, no forked bank module.
- **Ecosystem compatibility preserved.** The open zone behaves like any Cosmos chain. IBC counterparties, vanilla wallets, ecosystem tooling all work as expected.
- **Update via governance.** Changing a compliance rule is `MsgUpdateApproval` against `x/tokenization`. Approval criteria *are* the rules.
- **Mixed posture.** A chain can have compliant equity tokens *and* a public DEX *and* IBC-bridged stablecoins on the same chain, each in the appropriate zone.

## The boundary in detail

The boundary is implemented via two `x/tokenization` mechanisms:

- **`CosmosCoinBackedPath`** — a tokenization collection backed 1:1 by an underlying `sdk.Coin`. Wrapping is a tokenization transfer that locks the underlying and mints the wrapped token; unwrapping reverses it.
- **`CosmosCoinWrapperPath`** — a different mode for wrapping native Cosmos coins as tokenization-managed assets without a backed mint.

The wrap step runs through the same approval engine as any other tokenization transfer. That means at wrap time, the chain can check:

- **Sanctions** — is the wrapping address in a `DynamicStore` flagged as OFAC / SDN?
- **KYC** — does the address hold a `kyc-passport` badge from an authorized issuer?
- **Jurisdiction** — does the address hold a `jurisdiction:US` badge but the asset is restricted to non-US?
- **Threshold-based rules** — does the amount being wrapped exceed a FATF Travel Rule trigger?
- **External state** — via `EVMQueryChallenge`, query an external contract (e.g., a Chainalysis-tagged contract) for current address state.
- **Multi-sig** — via `VotingChallenge`, require N-of-M signers before the wrap completes, with optional `delayAfterQuorum` timelock.
- **Time** — via `transferTimes` and `AltTimeChecks`, permit wraps only during market hours, business days, or outside specific blackout windows.

Inside the zone, the same approval engine governs ongoing activity: transfer restrictions, holding periods (via `MustOwnTokens.ownershipTimes`), dividend distributions (via `IncrementedBalances` + `CoinTransfers`), multi-sig escrow (via `VotingChallenge` + `delayAfterQuorum`), time-bounded vesting (via `Balance.ownershipTimes`), etc.

## What the open zone looks like

The open zone is the chain's standard surface:

- **Native gas token** as `sdk.Coin` (e.g. `ubadge`)
- **IBC-arrived stablecoins** like USDC, USDT as ICS-20 vouchers
- **Vanilla bank transfers**, staking, governance (typically PoA in compliance contexts)
- **Public DEX activity**, lending pools, anything ecosystem-standard

The open zone is *not* unsupervised in the chain operator's eyes — they choose what counterparty chains they accept IBC from, what validators are in the active set (Proof of Authority), and what assets they permit at all. But it's not gated per-transfer.

## Comparison with the permissioned-token model

ERC-3643 puts compliance inside the token contract itself. Every transfer of every regulated token, anywhere the token goes on Ethereum, hits the contract's transfer-restriction logic. **The token is the boundary.**

This works on Ethereum because EVM has no native distinction between vanilla currency and regulated assets — everything is a contract call, so compliance has to live in the contract. There's no architectural seam to put a boundary at.

Cosmos has that seam. `sdk.Coin` and tokenization-managed assets are different first-class citizens. The compliance-zone model uses that distinction.

|  | Permissioned Token (ERC-3643 model) | Compliance Zone (BitBadges model) |
|---|---|---|
| **Where compliance lives** | In the token contract | In the approval engine |
| **What's gated** | Every transfer, everywhere | Boundary entry + intra-zone activity |
| **Adding a regime** | Deploy new contracts with new compliance modules | Configure new approval criteria; issue new badges |
| **Updating rules** | Redeploy or upgrade contracts | `MsgUpdateApproval` via governance |
| **Mixed activity** | All-or-nothing — token is permissioned wherever it goes | Two zones — vanilla Cosmos for the base layer, gated zone for regulated activity |
| **Native ecosystem compat** | Permissioned tokens can trip vanilla DeFi | Open zone behaves like any Cosmos chain |
| **Boundary semantics** | Implicit — the contract is the boundary | Explicit — wrap / unwrap operations |

The two models compose cleanly: the EVM precompile in `x/tokenization` lets ERC-3643 Solidity contracts inherit the same protocol-level gates the compliance zone enforces. A developer writing an ERC-3643 token on a BitBadges chain gets compliance-zone semantics for free, without redesigning their contract.

For a deeper standard-vs-interface comparison, see [BitBadges vs ERC-3643](bitbadges-vs-erc3643.md).

## Deploying the compliance zone

For chain developers building a BitBadges-based chain:

1. **Choose what enters the zone.** Decide which assets get wrapped at receipt (e.g., IBC-USDC → wrapped tokenization token) and which stay in the open zone (gas tokens, DEX-only assets).
2. **Configure the wrap-step approval criteria.** Sanctions registry, KYC requirement, jurisdiction map — whatever the regime needs. These are `x/tokenization` configurations, not code.
3. **Define intra-zone rules.** Holding periods, dividend distribution, redemption flow, vesting schedules — all expressed as `ApprovalCriteria` on the wrapped collections.
4. **Optionally restrict the open zone.** Channel-level IBC allowlists at chain config to refuse traffic from sanctioned counterparty chains. Validator-set policy via PoA. These are chain-config decisions, not module changes.
5. **Document the boundary.** Make it clear to users and counterparties what's in which zone — this is the chain's architectural contract.

No SDK fork. No plugin pack. No forked bank module. The compliance zone is a pattern you instantiate via `x/tokenization` configuration on a vanilla Cosmos chain.

## Further Reading

- [Cosmos Coin Wrapper Paths](../learn/cosmos-coin-wrapper-paths.md)
- [IBC Backed Minting](../learn/ibc-backed-minting.md)
- [Transferability and Approval Rules](../learn/transferability.md)
- [BitBadges vs ERC-3643](bitbadges-vs-erc3643.md)

# The Compliance Zone Architecture

Where compliance lives on a chain matters: in the token, the bank module, every smart contract, or at a controlled boundary. BitBadges chooses the boundary.

Two zones:

- **Open zone** — vanilla Cosmos. `sdk.Coin` denoms (gas, IBC stablecoins, bridge wrappers), public bank, standard staking, vanilla IBC. No compliance gates.
- **Compliance zone** — `x/tokenization` collections. A siloed environment under issuer sovereignty: the issuer configures transfer rules (sanctions, KYC, jurisdiction, holding periods, multi-sig escrow, rate limits); the approval engine enforces them.

The zones connect via approval-engine-mediated boundary operations: 1:1 wrapping, pool swaps, required side payments, IBC-backed mints. Every crossing runs the approval engine.

## Why two zones

Gating compliance at every layer (bank, IBC, staking, gov, every contract) has four problems:

- **Surface area** — compliance code spreads across modules; auditors verify each integration.
- **Ecosystem compat** — if bank rejects vanilla transfers, IBC counterparties can't predict behavior; wallets and indexers break.
- **Update friction** — rule changes mean modifying multiple call sites, possibly across forked SDK modules.
- **All-or-nothing** — can't mix compliant and permissionless assets on the same chain.

The compliance-zone model:

- **One audit surface** — boundary lives in `x/tokenization`. No SDK fork, no scattered ante decorators.
- **Ecosystem compat preserved** — open zone behaves like any Cosmos chain.
- **Update via governance** — `MsgUpdateApproval` against `x/tokenization`. Approval criteria *are* the rules.
- **Mixed posture** — compliant equity tokens, public DEX, IBC stablecoins on the same chain, each in the appropriate zone.

## Boundary mechanics

The boundary is a category of operations, not one primitive. Every crossing runs the approval engine. Patterns:

- **1:1 wrapping** — `CosmosCoinBackedPath` (collection backed 1:1 by underlying `sdk.Coin`; wrap locks the underlying and mints, unwrap reverses) or `CosmosCoinWrapperPath` (alternate mode without backed mint).
- **Pool swaps** — when a pool (x/gamm-style AMM) exchanges `sdk.Coin` for a tokenization asset, the approval engine gates the trade. No separate wrap step needed.
- **Required side payments** — `CoinTransfer` clauses on an approval move `sdk.Coin` alongside a tokenization transfer (royalties, redemption payouts, subscription fees).
- **IBC-backed minting** — packet receipt mints directly into a collection, with approval gates running on receipt.

At boundary time, the chain can check:

- **Sanctions** — address in a `DynamicStore` flagged OFAC / SDN?
- **KYC** — holds a `kyc-passport` badge from an authorized issuer?
- **Jurisdiction** — `jurisdiction:US` badge but asset is non-US?
- **Threshold** — amount exceeds a FATF Travel Rule trigger?
- **External state** — `EVMQueryChallenge` against an external contract (e.g., Chainalysis-tagged).
- **Multi-sig** — `VotingChallenge`, N-of-M signers, optional `delayAfterQuorum` timelock.
- **Time** — `transferTimes` + `AltTimeChecks` for market hours, business days, blackout windows.

Inside the zone, the same engine governs ongoing activity: transfer restrictions, holding periods (`MustOwnTokens.ownershipTimes`), dividends (`IncrementedBalances` + `CoinTransfers`), multi-sig escrow (`VotingChallenge` + `delayAfterQuorum`), vesting (`Balance.ownershipTimes`).

## The open zone

The chain's standard surface:

- Native gas token as `sdk.Coin` (e.g. `ubadge`)
- IBC-arrived stablecoins (USDC, USDT) as ICS-20 vouchers
- Vanilla bank, staking, governance (typically PoA)
- Public DEX, lending pools, ecosystem-standard activity

Supervised at chain-config level (counterparty allowlists, validator set, permitted assets) but not gated per-transfer.

## Cross-chain via siloed environments

`x/tokenization` is a siloed environment. The issuer is sovereign inside it: they write the rules, the approval engine enforces them.

Tokenization tokens can't travel over vanilla IBC — vanilla IBC moves `sdk.Coin` with no compliance semantics. The cross-chain pattern:

1. **Exit the source silo** — unwrap, redeem, or burn. Issuer exit rules fire; underlying `sdk.Coin` releases (ATOM, USDC, whatever backs the silo).
2. **Travel as `sdk.Coin` over vanilla ICS-20** — no custom channels, no middleware. Counterparties see normal IBC traffic.
3. **Re-enter a silo on the destination chain** — destination issuer's entry rules fire; mint into the destination collection.

Each silo enforces its own rules; the cross-chain hop is neutral IBC. Result:

- **IBC compatibility** — no custom protocols, no counterparty changes.
- **Issuer sovereignty** — each silo's rules are independent; no cross-issuer coordination.
- **Boundary-only enforcement** — siloed state only under approved conditions; in-transit, vanilla `sdk.Coin` has no semantics to violate.

Mirrors how tokenized securities cross jurisdictions in TradFi: delivered to a destination depository that re-applies its own regulatory framework.

## Compared to the permissioned-token model

ERC-3643 puts compliance in the token contract — every transfer everywhere hits transfer-restriction logic. The token *is* the boundary.

EVM has no architectural seam between vanilla currency and regulated assets, so compliance must live in the contract. Cosmos has that seam — `sdk.Coin` and tokenization-managed assets are distinct first-class citizens. The compliance-zone model uses it.

|  | Permissioned Token (ERC-3643) | Compliance Zone (BitBadges) |
|---|---|---|
| **Where compliance lives** | Token contract | Approval engine |
| **What's gated** | Every transfer, everywhere | Boundary entry + intra-zone activity |
| **New regime** | Deploy new contracts + compliance modules | Configure new approval criteria; issue new badges |
| **Updating rules** | Redeploy or upgrade contracts | `MsgUpdateApproval` via governance |
| **Mixed activity** | All-or-nothing | Two zones |
| **Ecosystem compat** | Permissioned tokens trip vanilla DeFi | Open zone is vanilla Cosmos |
| **Boundary** | Implicit (contract is the boundary) | Explicit (approval-engine ops into a silo) |

The two compose: `x/tokenization`'s EVM precompile lets ERC-3643 Solidity contracts inherit the compliance zone's gates without redesign. See [BitBadges vs ERC-3643](bitbadges-vs-erc3643.md).

## Deployment

For chain devs:

1. **Choose what enters the zone** — which assets wrap on receipt (IBC-USDC → wrapped token), which stay open (gas, DEX-only).
2. **Configure wrap-step approval criteria** — sanctions, KYC, jurisdiction. Configurations, not code.
3. **Define intra-zone rules** — holding periods, dividends, redemption, vesting. All `ApprovalCriteria`.
4. **Optionally restrict the open zone** — IBC channel allowlists, PoA validator set. Chain-config, not module changes.
5. **Document the boundary** — what's in which zone. The chain's architectural contract.

No SDK fork. No plugin pack. The compliance zone is a configuration pattern on vanilla Cosmos.

## Further Reading

- [Cosmos Coin Wrapper Paths](../learn/cosmos-coin-wrapper-paths.md)
- [IBC Backed Minting](../learn/ibc-backed-minting.md)
- [Transferability and Approval Rules](../learn/transferability.md)
- [BitBadges vs ERC-3643](bitbadges-vs-erc3643.md)

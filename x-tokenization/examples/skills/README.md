# 🤖 Builder Skills

These pages document every guided build skill available in the BitBadges Builder (shipped as part of [bitbadgesjs-sdk](https://github.com/BitBadges/bitbadgesjs)). Each skill provides step-by-step instructions for building a specific type of token or configuring a specific feature.

> **Tip:** If you're using the BitBadges builder in Claude, Cursor, or another AI tool via the MCP server (`npx bitbadgesjs-sdk-mcp`) or the `bitbadges-cli builder` command surface, these instructions are loaded automatically when you select a skill. These pages are provided as a human-readable reference.

## Token Types

- [Smart Token](smart-token.md) — IBC-backed smart token with 1:1 backing and two required approvals (backing + unbacking)
- [Fungible Token](fungible-token.md) — Simple fungible token with fixed or unlimited supply and configurable mint/transfer approvals
- [NFT Collection](nft-collection.md) — Non-fungible token collection with unique token IDs, metadata URIs, and badge-based ownership
- [Subscription](subscription.md) — Time-based subscription token with recurring payment approvals and auto-deletion on expiry
- [Custom 2FA](custom-2fa.md) — Two-factor authentication for transfers using a secondary approval address
- [Address List](address-list.md) — On-chain managed address list where membership = owning x1 of token ID 1
- [Payment Protocol](payment-protocol.md) — Invoices, escrows, bounties, milestones, and multi-party agreements using coinTransfer-based approvals or IBC-backed smart token escrow
- [Credit Token](credit-token.md) — Increment-only, non-transferable credit token purchased with any ICS20 denom. Users pay X of a denom and receive Y tokens as credits/proof of payment. For a 1:1 backed token with on-chain transferability, use the Smart Token standard instead.
- [Address List](address-list.md) — On-chain address list where membership = owning x1 of token ID 1. Manager can add/remove addresses.
- [Quest](quest.md) — Quest/reward collection — users complete criteria and claim a badge + coin payout
- [Prediction Market](prediction-market.md) — Binary prediction market with YES/NO outcome tokens, liquidity pool trading, and vote-based settlement
- [Bounty](bounty.md) — Escrow-based bounty with verifier arbitration. Submitter escrows coins, verifier accepts (pays recipient) or denies (refunds submitter). Expires if no decision.
- [Crowdfund](crowdfund.md) — On-chain crowdfunding with goal tracking via mustOwnTokens. Contributors deposit funds, receive refund tokens. Crowdfunder withdraws if goal met, contributors refund if not.
- [Auction](auction.md) — Single-item auction with intent-based bidding. Seller mints NFT directly to the winning bidder during the accept window.
- [Products](product-catalog.md) — Multi-product storefront with per-product pricing, supply limits, and optional burn-on-purchase. Each product is a separate token ID.

## Standards

- [Liquidity Pools](liquidity-pools.md) — Liquidity pool standard with the "Liquidity Pools" protocol standard tag
- [Tradable NFTs](tradable.md) — NFT marketplace standard enabling peer-to-peer transfers with the "NFTMarketplace" standard tag and NFTPricingDenom

## Approval Patterns

- [Minting](minting.md) — Mint approval patterns including public mint, whitelist mint, creator-only mint, payment-gated mint, and escrow payouts
- [Burnable](burnable.md) — Allow token holders to burn tokens by sending them to the burn address, permanently removing them from circulation
- [Multi-Sig / Voting](multi-sig-voting.md) — Require weighted quorum voting from multiple parties before transfers can proceed (multi-sig, governance, etc.)

## Features

- [BB-402 Token-Gated Access](bb-402.md) — Token-gated access protocol where ownership of specific badges grants API/resource access
- [Auto-Mint](auto-mint.md) — Mint and distribute tokens to recipients at collection creation time using MsgTransferTokens

## Advanced

- [Transferability & Update Rules](immutability.md) — Lock collection permissions to make properties permanently immutable or permanently permitted


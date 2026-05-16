# Standards Commands

End-user surface for every BitBadges standard. Each command group lets you **read** collections of that standard from the indexer and **emit** the right transaction msg for the user-side action — bid on an auction, pay a payment-request, redeem a credit-token tier, etc.

Two halves to know about:

1. **Read verbs** — `list`, `show`, `status`. Pure indexer queries, no signing.
2. **Action verbs** — emit a ready-to-sign msg JSON to stdout. Pipe into [`deploy`](deploy-commands.md) to broadcast.

The **create** side of every standard (the manager/creator setting up an auction, posting a payment-request, etc.) lives under [`build`](build-commands.md). The commands on this page cover the **consumer / participant** side.

```bash
# Read
bb auctions list                          # browse active auctions
bb auctions show 42                       # full details for one
bb auctions status 42                     # quick status: bidding / accepting / sold / expired

# Act (emit + deploy)
bb auctions place-bid 42 \
  --creator bb1... --amount 5000 --denom uusdc \
  | bb deploy --browser --msg-stdin
```

Every action verb emits the universal `{ok, data, ...}` envelope on stdout. Pipe it straight into `bb deploy` — deploy unwraps the envelope transparently. Use `--quiet` (or `BB_QUIET=1`) to suppress the human-readable auto-review banner on stderr; standard [build flags](build-commands.md#common-flags) (`--explain`, `--simulate`, `--dry-run`, etc.) all apply.

## Available Standards

| Standard | Command | What users do |
| --- | --- | --- |
| **Auctions** | `bb auctions` | `list / show / status / place-bid / cancel-bid / accept-bid / build` |
| **Bounties** | `bb bounties` | `list / show / status / accept / deny / claim-refund / build`. Accept/deny emit a 2-msg envelope (cast-vote + transfer). |
| **Credit Tokens** | `bb credit-tokens` | `list / show / purchase / build`. Tier amounts use `bigint`. |
| **Custom 2FA** | `bb custom-2fa` | `mint`. Create the collection with `bb build custom-2fa`, then `mint` issues short-lived 2FA tokens (5m default). The lifetime is encoded at mint time — minting without the helper yields tokens that never expire. |
| **Dynamic Stores** | `bb dynamic-stores` | On-chain key→bool maps. `create / update / delete / add / remove / set-value / get-value / list-values / search`. |
| **Intents** | `bb intents` | Off-chain OTC swap offers via user outgoing approvals. `list / show / create / fill / cancel`. |
| **NFTs** (orderbook) | `bb nfts` | `bid / list / cancel / buy / sell / orders / history / build`. Orderbook trading on individual badges. |
| **Payment Requests** | `bb pay-requests` | `list / show / status / pay / deny / claim-refund / build`. The no-escrow inverse of bounty — recipient asks payer to approve. |
| **Prediction Markets** | `bb prediction-markets` | `list / show / status / buy / sell / cancel / deposit / redeem / resolve / build`. YES/NO share trading. |
| **Products** | `bb products` | `list / show / purchase / build`. Product-catalog purchase flow. |
| **Smart Tokens** | `bb smart-tokens` | `list / show / status / deposit / withdraw / build`. The unified primitive behind vaults, AI-agent vaults, and tradable wrapped tokens. |
| **Subscriptions** | `bb subscriptions` | `list / status / claim / enable-renewal / cancel / subscribe`. Recurring-payment approvals. |
| **Swap / DEX** | `bb swap` | Cross-chain swap helpers — `assets / chains / balances / estimate / track / activity / intents`. Powered by Skip:Go via the indexer's consolidated `/swap/*` routes. |

## Status semantics

Every standard with a lifecycle (`auctions`, `prediction-markets`, `bounties`, `pay-requests`) exposes a `status` subcommand that returns one of a fixed set of values. Read these BEFORE acting — most action verbs throw if the standard isn't in a compatible state.

| Standard | Status values |
| --- | --- |
| Auctions | `bidding`, `accepting`, `sold`, `expired` |
| Bounties | `open`, `accepting`, `accepted`, `denied`, `expired` |
| Payment Requests | `pending`, `paid`, `denied`, `expired` |
| Prediction Markets | `active`, `closed`, `resolved`, `canceled` |
| Subscriptions | `active`, `renewable`, `cancelled` |

## Pattern: list → show → act

The agentic pattern most users follow:

```bash
# 1. Discover what's available
bb auctions list --output json | jq '.[] | {id, status, sellerAddress}'

# 2. Drill into one
bb auctions show 42

# 3. Check it's actionable
bb auctions status 42      # → must be "bidding" before place-bid

# 4. Emit + sign + broadcast
bb auctions place-bid 42 \
  --creator bb1my... --amount 5000 --denom uusdc \
  --quiet \
  | bb deploy --browser --msg-stdin
```

## Read commands hit the indexer

Every `list / show / status` reads from the BitBadges indexer (`api.bitbadges.io` by default). The CLI accepts the standard network flags inherited from [api-commands](api-commands.md):

```bash
bb auctions list                    # mainnet (default)
bb auctions list --mainnet          # explicit
bb auctions list --local            # localhost:3001
bb auctions list --url <custom>     # explicit base URL
```

## Action commands need signing

Every action verb (`place-bid`, `contribute`, `pay`, `purchase`, `redeem`, etc.) emits a `MsgSet*Approval` / `MsgTransferTokens` JSON to stdout. You then sign + broadcast through one of:

- **Browser wallet** (Keplr / MetaMask):
  ```bash
  bb auctions place-bid 42 ... | bb deploy --browser --msg-stdin
  ```
- **Local keyring** (bitbadgeschaind key):
  ```bash
  bb auctions place-bid 42 ... > /tmp/bid.json
  bb deploy --with-keyring --from mykey --msg-file /tmp/bid.json
  ```
- **Custom signer** (ethers / viem / HSM): pipe through `gen-tx-payload`:
  ```bash
  bb auctions place-bid 42 ... | bb gen-tx-payload --from bb1...
  ```

See [Deploy Commands](deploy-commands.md) for the full signing-path matrix.

## Reference

Every command has `--help` with the full flag surface. Required vs optional flags are now rendered as separate sections (`Required:` first, then `Options:` with sub-groups for `Metadata`, `Output`, `Network`, `Builder`, `Deploy`):

```bash
bb auctions place-bid --help
bb pay-requests pay --help
```

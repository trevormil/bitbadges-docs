# Build Commands

The `bitbadges-cli build` command provides 18 builders that generate ready-to-sign transaction JSON. Each builder creates a complete collection or approval configuration from simple, human-readable parameters.

All build commands accept friendly inputs -- use coin symbols like `USDC` instead of raw IBC denominations, and duration shorthands like `30d` instead of millisecond timestamps. The output is a fully-formed transaction message that can be signed and broadcast using the SDK, the BitBadges frontend, or the chain binary.

> **Tip — don't want to bring your own wallet?** Pipe the output of any collection builder straight into [`deploy --burner`](deploy-commands.md). The CLI generates a throwaway signer, funds it from the faucet, broadcasts the create-collection tx, and hands ownership to the address you pass as `--manager`. No keys to set up.

> **No IPFS hosting needed.** Every template accepts metadata in one of two modes per metadata-bearing entity: pass `--uri <pre-hosted-uri>` if you have already hosted the JSON yourself, or pass the per-entity field flags (`--name`, `--image`, `--description`) and the CLI serializes them into the on-chain `customData` field. The indexer, SDK, and frontend parse `customData` on read and surface the result as the resolved metadata, so there is no Pinata account to set up. Approvals are text-only — `--name` + `--description`, no image. The CLI throws a clear error if neither mode is fully satisfied; there are no defaults or placeholders. See [Collection Configuration › Inline metadata via customData](../../token-standard/learn/collection-setup-fields.md#inline-metadata-via-customdata) for the on-chain shape.

## Common Flags

Every build command supports the following flags:

| Flag | Description |
|------|-------------|
| `--dry-run` | Validate the output against standard compliance checks (results printed to stderr) |
| `--explain` | Print a human-readable explanation of the generated transaction (to stderr) |
| `--creator <address>` | Set the creator/sender address |
| `--manager <address>` | Set the collection manager address |
| `--json <input>` | Pass all parameters as JSON (inline, file path, or `-` for stdin). Overrides individual flags |
| `--condensed` | Output compact JSON with no whitespace |
| `--output-file <path>` | Write output to a file instead of stdout |

## Collection Builders

These commands generate `MsgUniversalUpdateCollection` transaction JSON for creating new collections.

### `build vault`

Create an IBC-backed vault token with optional withdrawal limits, 2FA gating, and emergency recovery.

```bash
bitbadges-cli build vault --backing-coin USDC \
  --name "My Vault" \
  --symbol vUSDC \
  --daily-withdraw-limit 1000 \
  --explain
```

| Flag | Required | Description |
|------|----------|-------------|
| `--backing-coin <symbol>` | Yes | Backing coin symbol (USDC, BADGE, ATOM, OSMO) |
| `--name <name>` | No | Collection name (default: "Vault") |
| `--symbol <symbol>` | No | Display symbol (e.g., vUSDC) |
| `--image <url>` | No | Image URL |
| `--description <text>` | No | Description |
| `--daily-withdraw-limit <n>` | No | Max daily withdrawal in display units |
| `--require-2fa <collectionId>` | No | 2FA collection ID for withdrawal gating |
| `--emergency-recovery <address>` | No | Recovery address for emergency migration |

### `build subscription`

Create a recurring subscription collection with configurable intervals, pricing, and optional multi-tier support.

```bash
bitbadges-cli build subscription --interval monthly \
  --price 10 --denom USDC --recipient bb1... \
  --tiers 3 --transferable
```

| Flag | Required | Description |
|------|----------|-------------|
| `--interval <duration>` | Yes | Interval: daily, monthly, annually, or shorthand (30d) |
| `--price <amount>` | No | Price per interval in display units |
| `--denom <symbol>` | No | Payment coin (USDC, BADGE) |
| `--recipient <address>` | No | Payout address |
| `--payouts <json>` | No | Multiple payouts: `[{"recipient","amount","denom"}]` |
| `--tiers <n>` | No | Number of tiers (default: 1) |
| `--transferable` | No | Allow post-mint transfers between users |
| `--name <name>` | No | Collection name (default: "Subscription") |

### `build bounty`

Create a bounty with escrowed funds, a verifier who approves completion, and a designated recipient.

```bash
bitbadges-cli build bounty --amount 500 --denom USDC \
  --verifier bb1... --recipient bb1... --expiration 30d
```

| Flag | Required | Description |
|------|----------|-------------|
| `--amount <n>` | Yes | Bounty amount in display units |
| `--denom <symbol>` | Yes | Coin (USDC, BADGE) |
| `--verifier <address>` | Yes | Verifier address |
| `--recipient <address>` | Yes | Recipient address |
| `--expiration <duration>` | No | Expiration duration (default: 30d) |
| `--name <name>` | No | Collection name (default: "Bounty") |

### `build payment-request`

Create an agent-initiated payment request — the inverse of `bounty`. The agent (or any address) creates a collection requesting payment from a targeted payer; the payer approves AND pays from their own wallet in a single action. **No escrow up front.**

```bash
bitbadges-cli build payment-request \
  --amount 10 --denom USDC \
  --payer bb1payer... --recipient bb1agent... \
  --expiration 30d --name "Service charge" \
  --context "Agent X is requesting payment for completed deliverable Y under approved budget envelope of \$100/month."
```

| Flag | Required | Description |
|------|----------|-------------|
| `--amount <n>` | Yes | Payment amount in display units |
| `--denom <symbol>` | Yes | Coin (USDC, BADGE) |
| `--payer <address>` | Yes | Payer address (the human approver) |
| `--recipient <address>` | Yes | Recipient address (agent / merchant) |
| `--expiration <duration>` | No | Expiration duration (default: 30d) |
| `--name <name>` | No | Collection name (default: "Payment Request") |
| `--context <text>` | No | Rationale shown to the payer at approval time (≥100 chars recommended) |

See the full spec in [skills/payment-request](../../x-tokenization/examples/skills/payment-request.md).

### `build crowdfund`

Create a crowdfunding collection with a funding goal and deadline.

```bash
bitbadges-cli build crowdfund --goal 10000 --denom USDC \
  --crowdfunder bb1... --deadline 30d
```

| Flag | Required | Description |
|------|----------|-------------|
| `--goal <n>` | Yes | Funding goal in display units |
| `--denom <symbol>` | Yes | Coin (USDC, BADGE) |
| `--crowdfunder <address>` | No | Who receives funds on success |
| `--deadline <duration>` | No | Deadline duration (default: 30d) |
| `--name <name>` | No | Collection name (default: "Crowdfund") |

### `build auction`

Create an auction collection with configurable bidding and acceptance windows.

```bash
bitbadges-cli build auction --bid-deadline 7d --accept-window 7d \
  --name "Rare Item" --description "Limited edition collectible" \
  --seller bb1seller...
```

| Flag | Required | Description |
|------|----------|-------------|
| `--bid-deadline <duration>` | No | Bidding window (default: 7d) |
| `--accept-window <duration>` | No | Accept window after bidding ends (default: 7d) |
| `--name <name>` | No | Item name (default: "Auction") |
| `--description <text>` | No | Item description |
| `--image <url>` | No | Item image URL |
| `--seller <address>` | No | Seller address — only this address can accept the winning bid (defaults to `--creator`) |

### `build product-catalog`

Create a product catalog collection with multiple products, each having its own price and optional supply limit.

```bash
bitbadges-cli build product-catalog \
  --products '[{"name":"Widget","price":25,"denom":"USDC","maxSupply":100}]' \
  --store-address bb1...
```

| Flag | Required | Description |
|------|----------|-------------|
| `--products <json>` | Yes | Product array: `[{"name","price","denom","maxSupply?","burn?"}]` |
| `--store-address <address>` | Yes | Payment recipient address |
| `--name <name>` | No | Collection name (default: "Product Catalog") |

### `build prediction-market`

Create a binary prediction market (YES/NO outcome tokens) with a designated resolver.

```bash
bitbadges-cli build prediction-market --verifier bb1... \
  --denom USDC --name "Will X happen by 2027?"
```

| Flag | Required | Description |
|------|----------|-------------|
| `--verifier <address>` | Yes | Market resolver address |
| `--denom <symbol>` | No | Payment coin (default: USDC) |
| `--name <name>` | No | Market question (default: "Prediction Market") |
| `--description <text>` | No | Market details |
| `--image <url>` | No | Market image URL |

### `build smart-account`

Create an IBC-backed smart account with optional trading and AI agent vault support.

```bash
bitbadges-cli build smart-account --backing-coin USDC \
  --symbol sUSDC --tradable --ai-agent-vault
```

| Flag | Required | Description |
|------|----------|-------------|
| `--backing-coin <symbol>` | Yes | Backing coin (USDC, BADGE, ATOM, OSMO) |
| `--symbol <symbol>` | No | Display symbol |
| `--image <url>` | No | Token image URL |
| `--tradable` | No | Enable liquidity pool trading |
| `--ai-agent-vault` | No | Add AI Agent Vault standard tag |

### `build credit-token`

Create a credit or prepaid token where users pay to receive a configurable number of tokens.

```bash
bitbadges-cli build credit-token --payment-denom USDC \
  --recipient bb1... --symbol CREDIT --tokens-per-unit 100
```

| Flag | Required | Description |
|------|----------|-------------|
| `--payment-denom <symbol>` | Yes | Payment coin (USDC, BADGE) |
| `--recipient <address>` | Yes | Payment recipient |
| `--symbol <symbol>` | No | Token symbol (default: CREDIT) |
| `--tokens-per-unit <n>` | No | Tokens per 1 display unit of payment (default: 100) |
| `--name <name>` | No | Collection name (default: "Credit Token") |

### `build custom-2fa`

Create a custom 2FA token collection for use as a second authentication factor in other collections.

```bash
bitbadges-cli build custom-2fa --name "My 2FA Token" --burnable
```

| Flag | Required | Description |
|------|----------|-------------|
| `--name <name>` | Yes | Token name |
| `--image <url>` | No | Token image URL |
| `--description <text>` | No | Description |
| `--burnable` | No | Allow burning |
| `--transferable` | No | Allow post-mint transfers between users |

### `build quests`

Create a quest or reward collection where users claim token rewards up to a maximum number of claims.

```bash
bitbadges-cli build quests --reward 10 --denom USDC --max-claims 1000
```

| Flag | Required | Description |
|------|----------|-------------|
| `--reward <n>` | Yes | Reward per claim in display units |
| `--denom <symbol>` | Yes | Reward coin (USDC, BADGE) |
| `--max-claims <n>` | Yes | Maximum number of claims |
| `--name <name>` | No | Collection name (default: "Quest") |

### `build address-list`

Create an on-chain address list (not a token collection -- uses a separate message type).

```bash
bitbadges-cli build address-list --name "Allowlist" --description "Approved addresses"
```

| Flag | Required | Description |
|------|----------|-------------|
| `--name <name>` | Yes | List name |
| `--image <url>` | No | List image URL |
| `--description <text>` | No | Description |

## Approval Builders

These commands generate user-level approval messages for marketplace listings, bids, payments, and trading intents.

### `build intent`

Create an OTC swap intent (user outgoing approval) on the Intent Exchange.

```bash
bitbadges-cli build intent --address bb1... \
  --collection-id 5 \
  --pay-denom USDC --pay-amount 100 \
  --receive-denom BADGE --receive-amount 500 \
  --expiration 7d
```

| Flag | Required | Description |
|------|----------|-------------|
| `--address <address>` | Yes | Creator address |
| `--collection-id <id>` | Yes | Intent Exchange collection ID |
| `--pay-denom <symbol>` | Yes | What you send (USDC, BADGE) |
| `--pay-amount <n>` | Yes | Amount you send in display units |
| `--receive-denom <symbol>` | Yes | What you receive |
| `--receive-amount <n>` | Yes | Amount you receive in display units |
| `--expiration <duration>` | No | How long the intent stays open (default: 7d) |

### `build recurring-payment`

Create a recurring payment approval (user incoming approval) for subscription collections.

```bash
bitbadges-cli build recurring-payment --collection-id 10 \
  --amount 25 --denom USDC --interval monthly \
  --recipient bb1... --expiration 365d
```

| Flag | Required | Description |
|------|----------|-------------|
| `--collection-id <id>` | Yes | Subscription collection ID |
| `--amount <n>` | Yes | Payment amount per interval in display units |
| `--denom <symbol>` | Yes | Payment coin (USDC, BADGE) |
| `--interval <duration>` | Yes | Payment interval (daily, monthly, annually) |
| `--recipient <address>` | Yes | Who receives payments |
| `--expiration <duration>` | No | How long the subscription lasts (default: 365d) |

### `build listing`

Create a marketplace listing (user outgoing approval) to sell tokens from a collection.

```bash
bitbadges-cli build listing --address bb1... \
  --collection-id 7 --token-ids "1-5" \
  --price 50 --denom USDC --max-sales 1 --expiration 30d
```

| Flag | Required | Description |
|------|----------|-------------|
| `--address <address>` | Yes | Seller address |
| `--collection-id <id>` | Yes | Collection ID to list from |
| `--token-ids <range>` | Yes | Token ID range (e.g., "1-5" or "1") |
| `--price <n>` | Yes | Asking price in display units |
| `--denom <symbol>` | Yes | Price coin (USDC, BADGE) |
| `--max-sales <n>` | No | Maximum number of sales (default: 1) |
| `--expiration <duration>` | No | Listing duration (default: 30d) |

### `build bid`

Create a marketplace bid (user incoming approval) to buy tokens from a collection.

```bash
bitbadges-cli build bid --address bb1... \
  --collection-id 7 --token-ids "1-5" \
  --price 40 --denom USDC --expiration 7d
```

| Flag | Required | Description |
|------|----------|-------------|
| `--address <address>` | Yes | Bidder address |
| `--collection-id <id>` | Yes | Collection ID to bid on |
| `--token-ids <range>` | Yes | Token ID range (e.g., "1-5" or "1") |
| `--price <n>` | Yes | Bid price in display units |
| `--denom <symbol>` | Yes | Price coin (USDC, BADGE) |
| `--expiration <duration>` | No | Bid duration (default: 7d) |

### `build pm-sell-intent`

Create a prediction market sell intent (user outgoing approval) to sell outcome tokens.

```bash
bitbadges-cli build pm-sell-intent --address bb1... \
  --collection-id 12 --token yes \
  --amount 10 --price 50 --denom USDC
```

| Flag | Required | Description |
|------|----------|-------------|
| `--address <address>` | Yes | Seller address |
| `--collection-id <id>` | Yes | Prediction market collection ID |
| `--token <yes\|no>` | Yes | Which outcome token to sell |
| `--amount <n>` | Yes | Number of tokens to sell |
| `--price <n>` | Yes | Total payment amount in display units |
| `--denom <symbol>` | Yes | Payment coin (USDC, BADGE) |
| `--expiration <duration>` | No | How long the intent stays open (default: 7d) |

### `build pm-buy-intent`

Create a prediction market buy intent (user incoming approval) to buy outcome tokens.

```bash
bitbadges-cli build pm-buy-intent --address bb1... \
  --collection-id 12 --token no \
  --amount 10 --price 50 --denom USDC
```

| Flag | Required | Description |
|------|----------|-------------|
| `--address <address>` | Yes | Buyer address |
| `--collection-id <id>` | Yes | Prediction market collection ID |
| `--token <yes\|no>` | Yes | Which outcome token to buy |
| `--amount <n>` | Yes | Number of tokens to buy |
| `--price <n>` | Yes | Total payment amount in display units |
| `--denom <symbol>` | Yes | Payment coin (USDC, BADGE) |
| `--expiration <duration>` | No | How long the intent stays open (default: 7d) |

## JSON Input Mode

All commands support `--json` for passing parameters as a JSON object. This is useful for scripting, piping from other tools, or when an AI agent generates the parameters programmatically.

```bash
# Inline JSON
bitbadges-cli build vault --json '{"backingCoin":"USDC","name":"My Vault"}'

# From a file
bitbadges-cli build vault --json ./params.json

# From stdin
echo '{"backingCoin":"USDC"}' | bitbadges-cli build vault --json -
```

## Output

All build commands output transaction JSON to stdout. Use `--dry-run` and `--explain` to inspect the output before signing:

- **`--dry-run`** runs standard compliance checks and prints any violations to stderr
- **`--explain`** prints a human-readable summary of what the transaction does to stderr

The output JSON can be signed and broadcast using the [BitBadges SDK signing client](../bitbadges-blockchain/create-and-broadcast-txs/signing-client.md), the [BitBadges frontend](https://bitbadges.io), or the chain binary.

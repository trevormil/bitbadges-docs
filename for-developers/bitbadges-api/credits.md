# API Credits

BitBadges uses **API credits** (on-chain symbol `APITOKEN`) to pay for API requests and AI Builder runs. One balance, two products.

## How you pay

- **API requests:** 1 API credit per request. Flat rate, every route.
- **AI Builder:** varies by model. The builder shows the cost before you run it.
- **Exchange rate:** 1 USDC = 100,000 APITOKEN, so 100,000 API requests costs about $1.

No tiers. No monthly subscription. No credit card on file.

## Top up

The top-up flow lives inline in the Developer Portal:

1. Go to [https://bitbadges.io/developer](https://bitbadges.io/developer) → **API Keys** tab.
2. Enter a USDC amount, confirm the on-chain transaction.
3. Your balance updates once the on-chain transfer confirms.

The same card shows your current balance and a low-balance warning when you're close to running out. API credits are soulbound to the account that buys them — they power both your API calls and the AI Builder from one balance, but they cannot be transferred, sold, or moved to another account.

## Rate limit

Each account can send up to **10,000 requests per minute**. Well above normal use — mainly exists to cap damage from runaway loops. If you need a higher ceiling, reach out.

## When you run out

If your balance hits zero mid-request, the API returns `402 Payment Required` with a link back to the developer portal. Your API key stays valid — just top up and keep going.

## AI Builder pricing

AI Builder charges per model call, based on how many tokens the model reads and writes. The builder quotes the exact cost up front so there's no surprise burn. Credits spent on AI Builder come from the same balance as API credits — one pool, two products.

## Checking your balance from the API

```typescript
const { balance } = await BitBadgesApi.getCreditBalance();
// { onChainTotal: number, used: number, remaining: number }
```

Or via HTTP:

```
GET https://api.bitbadges.io/api/v0/credits/balance
```

## FAQ

**Can I refund unused credits?** No — API credits are non-refundable.

**Can I transfer credits to another account?** No. API credits are soulbound to the account that buys them — they cannot be transferred, sold, or moved.

**Do credits expire?** No.

**Is there a free tier?** Select read-only routes are public without an API key, rate-limited per IP. Bring your own key and credits for everything else.

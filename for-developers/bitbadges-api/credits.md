# Credits

BitBadges uses **BADGE CREDITS** to pay for API requests and AI Builder runs. Credits are an on-chain token you hold in your BitBadges account.

## How you pay

- **API requests:** 1 credit per request. Flat rate, every route.
- **AI Builder:** varies by model. The builder shows the cost before you run it.
- **Exchange rate:** 1 USDC = 100,000 credits, so 100,000 API requests costs about $1.

No tiers. No monthly subscription. No credit card on file.

## Top up

1. Go to [https://bitbadges.io/credits](https://bitbadges.io/credits).
2. Buy credits or receive a transfer from another account.
3. Your balance updates once the on-chain transfer confirms.

The `/credits` page also shows your current balance, your recent usage, and a low-balance warning when you're close to running out.

## Rate limit

Each account can send up to **10,000 requests per minute**. This is well above normal use and mainly exists to cap damage from runaway loops. If you need a higher ceiling, reach out.

## When you run out

If your balance hits zero mid-request, the API returns `402 Payment Required` with a link to the top-up page. Your API key stays valid — just top up and keep going.

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

**Can I refund unused credits?** No — credits are non-refundable on-chain tokens. You can transfer them to another account you control.

**Do credits expire?** No.

**Is there a free tier?** Select read-only routes are public without an API key, rate-limited per IP. Bring your own key and credits for everything else.

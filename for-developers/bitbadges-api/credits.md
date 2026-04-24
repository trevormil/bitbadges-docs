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

## Accounting semantics (for advanced integrators)

API credit accounting runs through a Redis hot-path with a background
Mongo reconcile — similar to Stripe, Twilio, and Cloudflare's metering
patterns. Practical implications:

- **Budget checks are real-time** (Redis INCR + compare, atomic). A
  request that would exceed your balance is rejected immediately.
- **Usage counters (account-level `used`, per-key `totalRequests` /
  `totalCreditsSpent` / `lastRequestAt`) are eventually consistent** —
  they sync every ~5 seconds. Dashboards lag by that much; budget
  enforcement does not.
- **Small overshoot is possible and safe.** To avoid blocking on
  flush-to-Mongo, the budget check tolerates up to a small slack
  amount (~500 credits) past your on-chain balance. You will never be
  charged more than you paid for; you just occasionally get a few
  extra credits' worth of service before the next 402.
- **Top-up propagation:** the dev portal invalidates the cache on a
  successful top-up, so new balance is visible immediately. If you
  top up via another path (direct chain transfer, CLI), budget
  refreshes within 5 minutes.

## When you run out

If your balance hits zero mid-request, the API returns `402 Payment Required` with a link back to the developer portal. Your API key stays valid — just top up and keep going.

## AI Builder pricing

AI Builder charges per model call, based on how many tokens the model reads and writes. The builder quotes the exact cost up front so there's no surprise burn. Credits spent on AI Builder come from the same balance as API credits — one pool, two products.

## Handling 402 responses

When an account has no API credits remaining, every API request responds with HTTP `402 Payment Required`:

```json
{
  "error": "Insufficient credits",
  "errorMessage": "Insufficient API credits. Top up at /developer?tab=apiKeys.",
  "topUpUrl": "/developer?tab=apiKeys",
  "onChainTotal": 100,
  "used": 100,
  "remaining": 0,
  "decimals": 6
}
```

`onChainTotal` / `used` / `remaining` are in **display APITOKEN** (1 credit = 1 API call). `decimals` tells you the on-chain base-unit scale (`base = display × 10^decimals`) in case you want to cross-reference raw chain state.

Your API key stays valid — catch the `402`, prompt the account owner to top up in the Developer Portal, and retry once the balance confirms on-chain.

## Checking your balance

The Developer Portal's **API Keys** tab shows your live balance and inline top-up. For programmatic checks, hit the balance endpoint (requires a signed-in session with `Full Access` scope):

```
GET https://api.bitbadges.io/api/v0/credits/balance
```

```json
{
  "onChainTotal": 100000,
  "used": 423,
  "remaining": 99577,
  "decimals": 6
}
```

Same unit convention as the 402 response: top-level numbers are display APITOKEN. The off-chain `used` counter is only exposed to the signed-in account owner — the 402 response is the simplest way to surface it programmatically without the auth dance.

## FAQ

**Can I refund unused credits?** No — API credits are non-refundable.

**Can I transfer credits to another account?** No. API credits are soulbound to the account that buys them — they cannot be transferred, sold, or moved.

**Do credits expire?** No.

**Is there a free tier?** Select read-only routes are public without an API key, rate-limited per IP. Bring your own key and credits for everything else.

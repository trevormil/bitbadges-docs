# Smart Token-Type Detection

The AI Builder can auto-pick a single token-type skill from the user's prompt — so callers don't have to classify the 15 marketplace token types by hand. It's on by default, runs only when no token type is already selected, and gracefully returns *no pick* (freestyle build) when the match isn't confident.

Available in three surfaces:
- **`bitbadges.io/create` (no-code UI)** — the **Smart Detect** toggle next to "Add Plugin".
- **`POST /api/v0/builder/ai-build` (indexer HTTP)** — optional `autoInferTokenType` on the request body; `inferredTokenType` on the response.
- **`BitBadgesBuilderAgent` (programmatic SDK)** — `autoInferTokenType` option on the constructor and per-build.

## When it runs

Inference fires only when **both** hold:
1. The caller's `selectedSkills` has **no token-type skill** in it. Non-token-type skills (community plugins, additional-context) do not block inference — they coexist with the auto-pick.
2. `autoInferTokenType` is `true` (the default) — or unspecified.

If either fails, inference is skipped and `inferredTokenType` comes back as `undefined`. Explicit caller picks always win.

## How the pick is made

Two signals, in order. The first confident answer wins.

### 1. Deterministic standards → skill mapping

If an existing collection is available — the session transaction for `refine` mode, or an on-chain snapshot for `update` mode — and that collection already declares `standards: ["Smart Token"]` (or any other known standard), the agent maps directly to the matching token-type skill. **No LLM call.** This is the dominant path for refinement turns where the user's prompt is short ("fix this", "raise the supply") and can't be classified on its own.

Full mapping:

| `standards[]` value | Token-type skill id |
|---|---|
| `"Smart Token"` | `smart-token` |
| `"Fungible Token"` | `fungible-token` |
| `"NFT Collection"` | `nft-collection` |
| `"Subscription"` | `subscription` |
| `"Custom 2FA"` | `custom-2fa` |
| `"Payment Protocol"` | `payment-protocol` |
| `"Credit Token"` | `credit-token` |
| `"Address List"` | `address-list` |
| `"Quest"` | `quest` |
| `"Bounty"` | `bounty` |
| `"PaymentRequest"` | `payment-request` |
| `"Crowdfund"` | `crowdfund` |
| `"Auction"` | `auction` |
| `"Products"` | `product-catalog` |
| `"Prediction Market"` | `prediction-market` |
| `"Liquidity Pools"` | `liquidity-pools` |

Source of truth: `STANDARD_TO_TOKEN_TYPE` exported from `bitbadges/builder/agent`.

### 2. Haiku classifier (LLM fallback)

When the standards fast-path returns no match, one Claude Haiku call classifies the prompt against the full token-type catalog (the 15 skills above). The system prompt carries:

- Every token-type skill id + name + one-line description
- A strict JSON output contract: `{ id, confidence, reasoning }`
- Explicit permission to return `id: null` when the request is vague, covers multiple types, or describes something unusual

**Only `confidence: "high"` picks are acted on.** Anything else — low confidence, malformed JSON, unknown id, timeout, network error — collapses to `{ tokenType: null }` and the build proceeds freestyle. That's the intended fallback: a freestyle build with no token-type skill is always a valid outcome.

For `refine` / `update` modes, the user message sent to Haiku is enriched with:
- The original build intent (first-turn prompt)
- The last 3 refinement turns (so "fix this" doesn't strip the classifier of its signal)
- Any existing standards (double-declared if the fast-path didn't fire)

## SDK usage

```ts
import { BitBadgesBuilderAgent } from 'bitbadges/builder/agent';

const agent = new BitBadgesBuilderAgent({
  anthropicKey: process.env.ANTHROPIC_API_KEY,
  autoInferTokenType: true   // default — omit to opt in; set false to disable
});

const result = await agent.build('monthly subscription for $10, max 500 subscribers');

result.inferredTokenType;          // 'subscription'
result.inferredTokenTypeSource;    // 'llm'
result.inferredTokenTypeReasoning; // 'Recurring monthly payment pattern with a subscriber cap'
```

Per-build override:

```ts
await agent.build('whatever', {
  autoInferTokenType: false   // force off for this build only
});
```

Result shape:

| Field | Meaning |
|---|---|
| `inferredTokenType: string` | A token-type skill id — `'subscription'`, `'smart-token'`, etc. |
| `inferredTokenType: null` | Inference ran but no confident match. Build ran freestyle. |
| `inferredTokenType: undefined` | Inference was skipped — caller already supplied a token-type, or turned `autoInferTokenType` off. |
| `inferredTokenTypeSource: 'standards'` | Picked via the deterministic existing-collection fast-path. No LLM tokens spent. |
| `inferredTokenTypeSource: 'llm'` | Picked by Haiku. Tokens folded into the build's `onTokenUsage` with `round: 0`. |
| `inferredTokenTypeSource: undefined` | No pick (freestyle or skipped). |
| `inferredTokenTypeReasoning` | One-sentence rationale, suitable for display in a UI badge. |

## Indexer HTTP surface

`POST /api/v0/builder/ai-build`:

```json
{
  "prompt": "monthly subscription for $10",
  "selectedSkills": [],
  "autoInferTokenType": true
}
```

Response (additive, all three fields optional):

```json
{
  "success": true,
  "transaction": { "...": "..." },
  "inferredTokenType": "subscription",
  "inferredTokenTypeSource": "llm",
  "inferredTokenTypeReasoning": "Recurring monthly payment pattern"
}
```

`autoInferTokenType: false` suppresses inference even when `selectedSkills` is empty. Omit the field to accept the SDK default (on).

## Cost

- **Standards fast-path:** zero LLM cost — it's a table lookup.
- **Haiku classifier:** one call per build, bounded to 200 output tokens. The full token-type catalog (~1.5k input tokens) is marked with `cache_control` in the system prompt — second and subsequent inferences read from the prompt cache at 10% of the base rate. Folded into the caller's `onTokenUsage` hook as `round: 0`, so quota enforcement covers it automatically.

## What it does *not* do

- **Multiple token-type picks.** At most one. If two look equally plausible, the classifier is supposed to return `null` — the system prompt explicitly says so.
- **Non-token-type skills.** Community skills, additional-context skills, and the BitBadges approval / feature / advanced skills are untouched by inference. Those remain user-driven.
- **Overriding an explicit pick.** If `selectedSkills` already contains a token-type id, inference is skipped and `inferredTokenType` comes back as `undefined`. Explicit always wins.
- **Guessing when uncertain.** The only accepted LLM confidence level is `"high"`. Anything else → `null` → freestyle build.

## Tuning

- **Disable entirely:** `autoInferTokenType: false` on the constructor or per build.
- **Restrict candidates:** the SDK's `skills` whitelist on `BitBadgesBuilderAgentOptions` narrows which token-type ids inference can pick from (it intersects the whitelist with the full token-type catalog).
- **Custom model:** advanced callers can override the default Haiku call by invoking `inferTokenTypeFromPrompt` directly and passing their own model / client.

## See also

- [Programmatic Agent](programmatic-agent.md) — the full `BitBadgesBuilderAgent` API
- [Skills marketplace](../../x-tokenization/examples/skills/README.md) — the 15 token-type skills in human-readable form

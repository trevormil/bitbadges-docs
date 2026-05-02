# Programmatic Agent (`BitBadgesBuilderAgent`)

Build BitBadges collections from natural-language prompts in Node/TypeScript — **you bring your own Anthropic or OpenAI API key**. BitBadges never sees your key, never proxies your requests.

This is the scriptable counterpart to the [MCP Builder Tools](builder-tools.md) path. Pick whichever fits:

| | No-code UI | MCP Builder | **Programmatic Agent** |
|---|---|---|---|
| Where it runs | `bitbadges.io/create` | Claude Desktop / Cursor / Claude Code | Your Node process |
| LLM key | BitBadges-managed (billed credits) | Your Claude subscription | **Your Anthropic or OpenAI key** |
| Good for | End users, one-off builds | Power users, exploratory work | Dapps, bots, games, CI, fine-tuning |

## Install

Install the SDK plus the LLM provider you want to use. Both providers ship as **optional peer dependencies** — install whichever you'll use; you don't need both.

```bash
# Anthropic (default)
npm install bitbadges @anthropic-ai/sdk

# OpenAI
npm install bitbadges openai
```

The SDK never bundles either provider — your key stays in your process.

> **Anthropic / OpenAI keys are required ONLY for `BitBadgesBuilderAgent`** —
> the Node-side self-driving build loop on this page. The MCP server
> (`bitbadges-builder` bin used by Cursor / Claude Desktop / Claude Code /
> Cline / OpenAI Codex / Gemini Code Assist) is model-agnostic and does **not**
> read these env vars. If you're just installing the MCP, skip ahead to
> [Builder Tools (MCP)](builder-tools.md) — your IDE / agent already provides
> the model.

```bash
# Pick one — Anthropic (default) or OpenAI
export ANTHROPIC_API_KEY=sk-ant-...
export OPENAI_API_KEY=sk-proj-...

# Optional — only needed if prompts trigger query/search/simulate tools
export BITBADGES_API_KEY=bb-...
```

## Zero-config

### Anthropic (default)

```ts
import { BitBadgesBuilderAgent } from 'bitbadges/builder/agent';

const agent = new BitBadgesBuilderAgent({ anthropicKey: process.env.ANTHROPIC_API_KEY });

const result = await agent.build('create a subscription token for $10/month, max 500 subscribers');

console.log(result.toString());
// BitBadgesBuilderAgent build: 2 message(s), valid, 18,204 tokens, $0.0732, 5 round(s)

console.log(result.transaction);
```

### OpenAI

```ts
import { BitBadgesBuilderAgent } from 'bitbadges/builder/agent';

const agent = new BitBadgesBuilderAgent({
  provider: 'openai',
  apiKey: process.env.OPENAI_API_KEY
});

const result = await agent.build('create a subscription token for $10/month, max 500 subscribers');
console.log(result.transaction);
```

Both providers run the **same self-driving loop** — same tools, same validation, same review pass, same auto-token-type inference. The dispatcher translates the Anthropic-style internal message format to/from OpenAI's chat-completions shape at the API boundary.

> **Token-type inference parity**: both providers run a fast classifier (Anthropic Haiku / OpenAI `gpt-4o-mini`) before each build to auto-pick the right token-type skill. OpenAI uses native structured outputs (`response_format: json_schema, strict: true`) so the JSON contract is server-enforced. The two paths are interchangeable — pick whichever matches your stack.

## Auth modes

### Anthropic

```ts
// 1. API key (most common)
new BitBadgesBuilderAgent({ anthropicKey: 'sk-ant-…' });

// 2. OAuth token — for Claude Code / Claude Pro flows
new BitBadgesBuilderAgent({ anthropicAuthToken: 'oauth-…' });

// 3. Pre-built Anthropic client — for custom retry/interceptor logic
import Anthropic from '@anthropic-ai/sdk';
new BitBadgesBuilderAgent({ anthropicClient: new Anthropic({ apiKey, baseURL: proxy }) });
```

### OpenAI

```ts
// 1. API key (most common)
new BitBadgesBuilderAgent({ provider: 'openai', apiKey: 'sk-proj-…' });

// 2. Custom base URL — for Azure OpenAI, proxies, gateways
new BitBadgesBuilderAgent({ provider: 'openai', apiKey, baseURL: 'https://your-proxy/v1' });

// 3. Pre-built OpenAI client — for custom retry/interceptor/Azure-AD logic
import OpenAI from 'openai';
new BitBadgesBuilderAgent({ provider: 'openai', providerClient: new OpenAI({ apiKey, baseURL }) });
```

Env vars are auto-read when no explicit creds are passed:
- **Anthropic**: `ANTHROPIC_API_KEY`, `ANTHROPIC_OAUTH_TOKEN`, `ANTHROPIC_AUTH_TOKEN`
- **OpenAI**: `OPENAI_API_KEY`
- **Shared**: `BITBADGES_API_KEY`, `BITBADGES_API_URL`

## Customization

```ts
const agent = new BitBadgesBuilderAgent({
  // Provider — pick one of the auth-mode patterns above
  anthropicKey: process.env.ANTHROPIC_API_KEY,
  bitbadgesApiKey: process.env.BITBADGES_API_KEY,
  model: 'sonnet',                       // Anthropic: 'haiku' | 'sonnet' (default) | 'opus'.
                                         // OpenAI: pass a model id directly, e.g. 'gpt-4o' / 'gpt-4o-mini'.
  validation: 'strict',                  // 'strict' | 'lenient' | 'off'
  skills: ['subscription', 'fungible-token'], // limit the skill set
  systemPromptAppend: 'Always use locked-approvals permissions.', // adds to base prompt
  maxRounds: 8,                          // agent loop cap
  fixLoopMaxRounds: 3,                   // validation fix cap
  autoInferTokenType: true,              // default — smart token-type detection; set false to disable
  sessionStore: new MemoryStore(),       // MemoryStore | FileStore | your own KVStore
  hooks: {
    onTokenUsage:   (u) => console.log(`$${u.cumulativeCostUsd.toFixed(4)}`),
    onToolCall:     (e) => console.log(`[${e.name}] ${e.durationMs}ms`),
    onStatusUpdate: (s) => showToUser(s),   // "Building…", "Validating…", etc.
    onLog:          (e) => writeToLogSink(e), // info / ai_text / validation / error
    onCompletion:   (trace) => saveTraceToDb(trace)
  },
  defaultCreatorAddress: 'bb1…',
  debug: false                           // set true to dump prompt/responses to stderr
});
```

### Hook contract

- **`onTokenUsage`** is **load-bearing**: it's awaited, and rejections propagate out of `build()`. Throw from it to enforce per-build quotas (the BitBadges indexer does this with its TokenLedger).
- **`onCompletion`** fires exactly once per `build()` — on success AND on error paths — so cleanup logic runs either way.
- **`onToolCall`** / **`onStatusUpdate`** / **`onLog`** are fire-and-forget observability hooks; rejections are swallowed so a misbehaving logger can't hang a build.
- **`onLog`** receives `{ type: 'info' | 'ai_text' | 'validation' | 'error', label, data }` entries — round boundaries, the LLM's text responses, validation-gate pass/fail. Useful for live-tail dev consoles and audit log persistence.

### Validation modes

- `'strict'` (default) — throws `ValidationFailedError` if hard errors remain after the fix loop.
- `'lenient'` — always returns; `result.valid` is false with hard errors surfaced in `result.errors`.
- `'off'` — skips the gate entirely. Use only for experimentation.

### Skills

Two inputs, two levels:

```ts
new BitBadgesBuilderAgent({
  anthropicKey,
  skills: ['nft', 'smart-token']          // whitelist — constructor-level filter
});

await agent.build('mint 100 nfts', {
  selectedSkills: ['nft']                  // actual injection for this build
});
```

`agent.listSkills()` returns every available skill (filtered by the constructor
whitelist when set). `agent.describeSkill(id)` returns one by ID. Discovery is
code-only — there's no public marketplace endpoint. If you pass an unknown ID
to `selectedSkills`, it's dropped silently (no build failure); enable `debug:
true` to log dropped IDs.

#### How skill content is injected by mode

| Build mode | What gets injected |
|---|---|
| `create` | **Full** skill instructions with build recipes — the LLM follows them to construct a new collection from scratch. |
| `update` / `refine` | **Summaries only**, with an explicit "don't rebuild the collection to match the skill" warning. The collection already exists on-chain; skills are reference context, not a blueprint. |

If you're hitting the agent for an update and seeing over-aggressive rewriting,
this is the dial to turn — drop skills from the per-build call entirely and the
agent falls back to generic `DOMAIN_KNOWLEDGE` guidance.

#### Smart token-type inference (auto-pick)

When the caller doesn't supply a token-type skill, the agent classifies the prompt and prepends one high-confidence pick — or builds freestyle if nothing is confidently a match. See the dedicated **[Smart Token-Type Detection](smart-token-type-detection.md)** page for the full contract.

Quick shape:

```ts
new BitBadgesBuilderAgent({
  anthropicKey,
  autoInferTokenType: true           // default — flip to false to disable
});

const result = await agent.build('monthly subscription for $10/mo');
result.inferredTokenType;            // 'subscription' — or null (freestyle), or undefined (skipped)
result.inferredTokenTypeSource;      // 'standards' (existing-collection fast-path) | 'llm'
result.inferredTokenTypeReasoning;   // one-sentence rationale
```

Inference is skipped entirely when `selectedSkills` already has a token-type entry — explicit picks win. Non-token-type skills (community, additional-context) don't block inference.

#### Community skills (power-user)

`promptSkillIds` injects community-contributed skill docs stored on BitBadges.
No discovery UI ships — callers bring their own IDs (shared via URL, Discord,
internal registry). Requires a BitBadges API key.

```ts
import { BitBadgesBuilderAgent, createBitBadgesCommunitySkillsFetcher } from 'bitbadges/builder/agent';

const agent = new BitBadgesBuilderAgent({
  anthropicKey,
  bitbadgesApiKey: process.env.BITBADGES_API_KEY,
  communitySkillsFetcher: createBitBadgesCommunitySkillsFetcher()
});

await agent.build(prompt, {
  selectedSkills:  ['subscription'],
  promptSkillIds:  ['community-skill-xyz-123']
});
```

Fetcher calls `GET /api/v0/builder/community-skills?ids=...` and silently
returns an empty array on any failure (missing key, network error, timeout) —
your build still runs, it just loses the community injection.

**Local dev:** when `bitbadgesApiUrl` points at `localhost`, `127.0.0.1`, or
`*.localhost`, the fetcher skips the API-key requirement. Mirrors the
indexer's own relaxed auth for local development — iterate against a
local BitBadges indexer without a production key.

### Prompt-injection guard on the system-prompt slots

`systemPromptAppend` (additive) and `systemPrompt` (full replace) both
run through an injection-pattern check at agent construction. If
either contains obvious "ignore all previous instructions" / "you are
now a…" style payloads, the constructor throws a
`BitBadgesBuilderAgentError` with code `INVALID_SYSTEM_PROMPT_APPEND` or
`INVALID_SYSTEM_PROMPT`. Hosted/server deployments that accept
end-user input into these slots should still run their own
`containsInjection` check at the trust boundary — the SDK's check is
a defense-in-depth, not a replacement.

### Custom tools

Add tools on top of the builtins, or filter them out:

```ts
new BitBadgesBuilderAgent({
  anthropicKey,
  tools: {
    remove: ['build_claim'],
    add: [{
      definition: {
        name: 'my_custom_tool',
        description: '…',
        input_schema: { type: 'object', properties: { foo: { type: 'string' } } }
      },
      execute: async (args, ctx) => ({ ok: true, echo: args })
    }]
  }
});
```

## Session stores

Conversation messages + token counters are persisted so refinement works across HTTP requests.

```ts
import { MemoryStore, FileStore } from 'bitbadges/builder/agent';

// Default — single-process, in-memory
new BitBadgesBuilderAgent({ anthropicKey, sessionStore: new MemoryStore() });

// Disk-backed — survives process restarts (default dir: ~/.bitbadges/agent-sessions)
new BitBadgesBuilderAgent({ anthropicKey, sessionStore: new FileStore({ dir: '/var/lib/bb' }) });

// BYO — any object matching the KVStore interface works
class RedisStore implements KVStore {
  async get(key)          { /* ... */ }
  async set(key, value)   { /* ... */ }
  async delete(key)       { /* ... */ }
}
new BitBadgesBuilderAgent({ anthropicKey, sessionStore: new RedisStore() });
```

Pass the same `sessionId` across `.build()` calls to continue a session (e.g., for refinement).

## Result shape

```ts
interface BuildResult {
  valid: boolean;
  transaction: any;              // parsed object — not a JSON string
  errors: StructuredError[];     // code, message, path?, fixHint?
  warnings: Warning[];           // non-fatal advisory notes
  advisoryNotes: string[];       // raw review findings
  validation: any;               // SDK validator result
  simulation: any | null;        // null when no simulator configured
  audit: any | null;             // review findings + summary
  tokensUsed: number;
  costUsd: number;               // always computed per selected model
  rounds: number;
  fixRounds: number;
  trace: BuildTrace;             // full messages + tool calls + prompt hash
  toString(): string;            // human-readable one-liner
}
```

Errors dispatch on `instanceof`:

```ts
import {
  BitBadgesBuilderAgentError,
  ValidationFailedError,
  QuotaExceededError,
  AnthropicAuthError,
  AbortedError,
  PeerDependencyError,
  SimulationError
} from 'bitbadges/builder/agent';
```

## Image placeholders

The agent has two image-handling modes. Pick whichever fits your pipeline.

### 1. Real URLs in the prompt (simplest)

If you already have hosted images, just tell the agent the URLs in the prompt.
The LLM emits them verbatim into `metadataPlaceholders` entries.

```ts
await agent.build(
  `Create an NFT collection with hero image https://cdn.example.com/hero.png and
   token art https://cdn.example.com/t1.png`
);
```

No post-processing needed. Downside: the LLM has to faithfully copy the URLs,
so keep them short and well-formed.

### 2. Placeholders + post-build substitution (for dynamically-uploaded images)

When the user is still choosing/uploading images at build time, use symbolic
placeholders and swap them in after the build:

```ts
const result = await agent.build(prompt, {
  availableImagePlaceholders: ['IMAGE_1', 'IMAGE_2']
});

const finalTx = agent.substituteImages(result.transaction, {
  IMAGE_1: 'https://cdn.example.com/hero.png',   // or a data: URL
  IMAGE_2: 'ipfs://bafy…'
});
```

The LLM wires `IMAGE_N` tokens into the metadata; you resolve them at the end.
Matches the hosted frontend's flow exactly.

### Detecting stragglers

`agent.collectImageReferences(tx)` returns every `IMAGE_N` token still in the
transaction. Useful as a pre-broadcast sanity check — anything that comes back
from this call is a placeholder that never got a real value and will land
on-chain as-is.

## Health check

```ts
const report = await agent.healthCheck();
// { anthropic: { ok: true, model: 'claude-sonnet-4-6' },
//   bitbadgesApi: { ok: true, configured: true } }
```

## Validate without building

```ts
const existing = loadTxFromDisk();
const { valid, errors, simulation } = await agent.validate(existing);
```

## Export as a single prompt for no-tools LLMs

When you want to hand the build to Claude.ai, ChatGPT, or Gemini (no tools
available there), use `agent.exportPrompt()` to assemble the no-tools
variant of the system prompt concatenated with the user message. The LLM
emits the final transaction JSON directly.

```ts
const { prompt, communitySkillsIncluded } = await agent.exportPrompt(
  'create a subscription token for $10/mo',
  { selectedSkills: ['subscription'] }
);

// Paste `prompt` into Claude.ai / ChatGPT / Gemini — the output will be
// a { messages: [{ typeUrl, value }] } JSON object ready to paste into
// the frontend's Paste Transaction step.
```

No Anthropic call is made. No validation, no simulation, no fix loop — this
is a pure prompt-assembly helper. Best-effort path; the SDK-agent `build()`
flow remains the quality-gated path.

## Abort

```ts
const controller = new AbortController();
const result = agent.build(prompt, { abortSignal: controller.signal });
setTimeout(() => controller.abort(), 30_000);
// or: agent.abort()
```

## Cancellation + streaming

- **Cancellation**: supported via `abortSignal` or `agent.abort()`.
- **Streaming**: not in v1. The agent returns when the build completes or throws; use `onTokenUsage` / `onToolCall` hooks for live progress.

## Prompt caching (automatic)

The agent uses Anthropic's prompt caching on the stable prefix — system
prompt, tool schemas, and inlined skill instructions — so subsequent
builds inside a 5-minute window read those tokens from cache at ~10% of
the regular input-token cost. Cache-creation tokens cost ~1.25x regular
input on the miss; one hit afterwards pays the miss back and every
hit after that is profit.

Caching is on by default. There's nothing to configure. Skill ordering
is canonicalized (alphabetical) so `['nft', 'subscription']` and
`['subscription', 'nft']` hit the same cache key.

### When caching actually pays off

The stable prefix (system prompt + tool schemas + inlined skills) is typically
10–15% of the per-build token count — the rest is dynamic user context and the
tool-calling round trips. Numbers to keep in mind:

- **First build with a new skill set**: cache miss. You pay 1.25x on the
  prefix tokens and nothing comes back. Net: slightly more expensive than
  no-cache, by a few cents at most for a typical build.
- **Second build within 5 minutes with the same skill set**: cache hit.
  The prefix tokens now cost 10% of full rate. Break-even against the miss
  hits roughly here.
- **Steady-state** (several builds / hour with overlapping skill sets):
  cache-read tokens dominate the input count on `result.trace`. Real-world
  savings are 40–60% on the total input-token bill, not 90% (because the
  prefix is only part of the request).

For one-off scripts that run a single build and exit, you pay the 1.25x write
premium with no recovery. The absolute cost delta is small (cents), so leave
caching on — but don't count on it as a headline optimization for low-volume
use.

### Observability

The `onTokenUsage` hook reports cache counters per round:

```ts
new BitBadgesBuilderAgent({
  anthropicKey,
  hooks: {
    onTokenUsage: (u) => {
      console.log(
        `round ${u.round}: ${u.inputTokens} in, ${u.outputTokens} out, ` +
        `cache ${u.cacheReadTokens} read / ${u.cacheCreationTokens} write, ` +
        `cumulative $${u.cumulativeCostUsd.toFixed(4)}`
      );
    }
  }
});
```

`result.trace.cacheReadTokens` and `result.trace.cacheCreationTokens`
also carry cumulative counts for the whole build. A healthy steady-
state setup has `cacheReadTokens >> inputTokens`.

### What invalidates the cache

- 5-minute TTL since the last hit.
- Any change to the system prompt (e.g. `systemPromptAppend` edit).
- Any change to the tool set (`tools.add` / `tools.remove`).
- Any change to the canonical skill set.

The per-request tail (request header, metadata, prompt text, refinement
history) is never cached — it's expected to vary.

## `/internals` — unstable primitives

For users who want to run their own loop (different LLM, custom strategy, fine-tuning data collection):

```ts
import {
  buildSystemPrompt, DOMAIN_KNOWLEDGE, SKILL_INSTRUCTIONS,
  runAgentLoop, runValidationGate, buildFixPrompt,
  createAgentToolRegistry
} from 'bitbadges/builder/internals';
```

**Not covered by semver** — anything here may be renamed or removed in a minor release. Use `bitbadges/builder/agent` (the stable path) whenever possible.

## Examples

Full runnable scripts at [bitbadgesjs/packages/bitbadgesjs-sdk/examples/builder-agent/](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk/examples/builder-agent):

- `zero-config.ts` — the 5-line sample
- `middle-tier.ts` — hooks, skills, file store, typed errors
- `diy-internals.ts` — OpenAI-via-`/internals` wiring (unsupported)

## Troubleshooting

- **`PeerDependencyError: @anthropic-ai/sdk is required`** — run `npm install @anthropic-ai/sdk`.
- **`Anthropic credentials are required`** — set `ANTHROPIC_API_KEY` or pass `anthropicKey`/`anthropicAuthToken` to the constructor.
- **`ValidationFailedError` after 3 fix rounds** — the validation fix loop gave up. Inspect `err.errors` for structured causes and `err.advisoryNotes` for design concerns the agent considered but didn't resolve. Raising `fixLoopMaxRounds` rarely helps — usually the prompt needs more constraints.
- **Simulation says `jsonToTxBytes` error** — this is an encode-time advisory, not a chain failure. The transaction is typically still broadcast-safe.

## See also

- [Builder Tools Reference](builder-tools.md) — the MCP path (same tools, different runtime).
- [CLI for AI Agents](../cli/for-ai-agents.md) — terminal-first workflow.
- [SDK README](https://github.com/BitBadges/bitbadgesjs/tree/main/packages/bitbadgesjs-sdk) — TypeScript library reference.

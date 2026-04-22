# Programmatic Agent (`BitBadgesAgent`)

Build BitBadges collections from natural-language prompts in Node/TypeScript — **you bring your own Anthropic API key**. BitBadges never sees your key, never proxies your requests.

This is the scriptable counterpart to the [MCP Builder Tools](builder-tools.md) path. Pick whichever fits:

| | No-code UI | MCP Builder | **Programmatic Agent** |
|---|---|---|---|
| Where it runs | `bitbadges.io/create` | Claude Desktop / Cursor / Claude Code | Your Node process |
| Anthropic key | BitBadges-managed (billed credits) | Your Claude subscription | **Your Anthropic API key** |
| Good for | End users, one-off builds | Power users, exploratory work | Dapps, bots, games, CI, fine-tuning |

## Install

```bash
npm install bitbadges @anthropic-ai/sdk
```

`@anthropic-ai/sdk` is an optional peerDependency — the SDK does not ship it. Install it yourself so your key stays in your process.

```bash
export ANTHROPIC_API_KEY=sk-ant-...
# Optional — only needed if prompts trigger query/search/simulate tools
export BITBADGES_API_KEY=bb-...
```

## Zero-config

```ts
import { BitBadgesAgent } from 'bitbadges/builder/agent';

const agent = new BitBadgesAgent({ anthropicKey: process.env.ANTHROPIC_API_KEY });

const result = await agent.build('create a subscription token for $10/month, max 500 subscribers');

console.log(result.toString());
// BitBadgesAgent build: 2 message(s), valid, 18,204 tokens, $0.0732, 5 round(s)

console.log(result.transaction);
```

## Auth modes

All three are interchangeable — pick whichever matches your deployment:

```ts
// 1. API key (most common)
new BitBadgesAgent({ anthropicKey: 'sk-ant-…' });

// 2. OAuth token — for Claude Code / Claude Pro flows
new BitBadgesAgent({ anthropicAuthToken: 'oauth-…' });

// 3. Pre-built Anthropic client — for custom retry/interceptor logic
import Anthropic from '@anthropic-ai/sdk';
new BitBadgesAgent({ anthropicClient: new Anthropic({ apiKey, baseURL: proxy }) });
```

Env vars are auto-read when no explicit creds are passed: `ANTHROPIC_API_KEY`, `ANTHROPIC_OAUTH_TOKEN`, `ANTHROPIC_AUTH_TOKEN`, `BITBADGES_API_KEY`, `BITBADGES_API_URL`.

## Customization

```ts
const agent = new BitBadgesAgent({
  anthropicKey: process.env.ANTHROPIC_API_KEY,
  bitbadgesApiKey: process.env.BITBADGES_API_KEY,
  model: 'sonnet',                       // 'haiku' | 'sonnet' (default) | 'opus'
  validation: 'strict',                  // 'strict' | 'lenient' | 'off'
  skills: ['subscription', 'fungible-token'], // limit the skill set
  systemPromptAppend: 'Always use locked-approvals permissions.', // adds to base prompt
  maxRounds: 8,                          // agent loop cap
  fixLoopMaxRounds: 3,                   // validation fix cap
  sessionStore: new MemoryStore(),       // MemoryStore | FileStore | your own KVStore
  hooks: {
    onTokenUsage:  (u) => console.log(`$${u.cumulativeCostUsd.toFixed(4)}`),
    onToolCall:    (e) => console.log(`[${e.name}] ${e.durationMs}ms`),
    onCompletion:  (trace) => saveTraceToDb(trace) // for analytics, fine-tuning data collection
  },
  defaultCreatorAddress: 'bb1…',
  debug: false                           // set true to dump prompt/responses to stderr
});
```

### Validation modes

- `'strict'` (default) — throws `ValidationFailedError` if hard errors remain after the fix loop.
- `'lenient'` — always returns; `result.valid` is false with hard errors surfaced in `result.errors`.
- `'off'` — skips the gate entirely. Use only for experimentation.

### Skills

Narrow the skill set injected into the system prompt:

```ts
new BitBadgesAgent({ anthropicKey, skills: ['nft', 'smart-token'] });
```

Full list: call `getAllSkillInstructions()` from `bitbadges/builder/skills` or see [Skill Instructions](#).

### Custom tools

Add tools on top of the builtins, or filter them out:

```ts
new BitBadgesAgent({
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
new BitBadgesAgent({ anthropicKey, sessionStore: new MemoryStore() });

// Disk-backed — survives process restarts (default dir: ~/.bitbadges/agent-sessions)
new BitBadgesAgent({ anthropicKey, sessionStore: new FileStore({ dir: '/var/lib/bb' }) });

// BYO — any object matching the KVStore interface works
class RedisStore implements KVStore {
  async get(key)          { /* ... */ }
  async set(key, value)   { /* ... */ }
  async delete(key)       { /* ... */ }
}
new BitBadgesAgent({ anthropicKey, sessionStore: new RedisStore() });
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
  BitBadgesAgentError,
  ValidationFailedError,
  QuotaExceededError,
  AnthropicAuthError,
  AbortedError,
  PeerDependencyError,
  SimulationError
} from 'bitbadges/builder/agent';
```

## Image placeholders

The agent emits `IMAGE_N` placeholders inside `metadataPlaceholders` sidecars. Substitute them with your own bytes/URLs:

```ts
const final = agent.substituteImages(result.transaction, {
  IMAGE_1: 'https://cdn.example.com/hero.png',
  IMAGE_2: 'data:image/png;base64,…'
});
```

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

# 🔨 Getting Started

BitBadges is proud to offer no-code / low-code flows for all our major services. Simply navigate to the Create tab in-site, and get started creating tokens, claims, address lists, explore listings, applications, and more!

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

> **Need help?** Join our Discord for support from the BitBadges team and community developers.
>
> **Need BADGE?** Contact us on Discord - get subsidized credits for developers during chaosnet!
>
> **No-Code / In-Site Solutions** Check out the [Create tab](https://bitbadges.io/create) or the [developer portal](https://bitbadges.io/developer) first to see what all is possible. Most of the time, you can just do everything with no code directly in-site! No need for any direct integration. Let us handle everything!
>
> Get creative. You can gate URLs, Discords, and integrate with many of your favorite tools without a single line of code!

## Explore First, Read Later

We strongly recommend, if you have not already, to explore the claim tester and other creation options in-site. Many of your questions should be answered by the interface and is much easier to understand than a bunch of long text here in this documentation. Just go explore and experiment first.

Most of your setup and management (and oftentimes all) will be done directly in-site via the developer portal or Create tab. Get started at [https://bitbadges.io/create](https://bitbadges.io/create).

## Quick Start - Tokens

{% content-ref url="../x-tokenization/" %}
[x-tokenization](../x-tokenization/)
{% endcontent-ref %}

## Quick Start - Claims

{% content-ref url="../overview/claim-builder/" %}
[claim-builder](../overview/claim-builder/)
{% endcontent-ref %}

## Quick Start - API

{% content-ref url="bitbadges-api/" %}
[bitbadges-api](bitbadges-api/)
{% endcontent-ref %}

{% content-ref url="bitbadges-sdk/" %}
[bitbadges-sdk](bitbadges-sdk/)
{% endcontent-ref %}

```bash
npm i bitbadgesjs-sdk
```

```ts
import { BitBadgesAPI } from 'bitbadgesjs-sdk';

const api = new BitBadgesAPI({
  ...
});

await BitBadgesAPI.getCollection(...);
```

## Quick Start - MCP Builder Tools (AI)

The BitBadges MCP server gives any AI assistant (Claude, Cursor, etc.) 30+ tools to build, audit, sign, and broadcast BitBadges transactions — no manual JSON required.

```bash
# Install globally
npm install -g bitbadges-builder-mcp

# Or run directly with npx
npx bitbadges-builder-mcp
```

**Claude Desktop** — add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "bitbadges-builder": {
      "command": "npx",
      "args": ["-y", "bitbadges-builder-mcp"],
      "env": {
        "BITBADGES_API_KEY": "your-api-key"
      }
    }
  }
}
```

**Claude Code:**

```bash
claude mcp add bitbadges-builder -- npx -y bitbadges-builder-mcp
```

See the full [MCP Builder Tools Reference](ai-agents/mcp-builder-tools.md) for all tools, schemas, and workflows. For bot/agent guides, see [AI Agents & Bots](ai-agents/).

## Developing with AI

Below, we provide some resources that may be helpful for developing with AI. If there is anything else we can do to make development easier, let us know!

| Resource | Link |
|----------|------|
| **MCP Builder Tools** | `npm i -g bitbadges-builder-mcp` — [Reference](ai-agents/mcp-builder-tools.md) |
| **BitBadges SDK** | `npm i bitbadgesjs-sdk` — [Docs](bitbadges-sdk/overview.md) |
| **AI Quickstarter Repo** | [github.com/BitBadges/bitbadges-quickstarter-ai](https://github.com/BitBadges/bitbadges-quickstarter-ai) |
| **GitBook MCP** | [docs.bitbadges.io/~gitbook/mcp](https://docs.bitbadges.io/~gitbook/mcp) — searchDocumentation tool |
| **API OpenAPI Spec** | [openapi.json](https://raw.githubusercontent.com/bitbadges/bitbadgesjs/main/packages/bitbadgesjs-sdk/openapi-hosted/openapi.json) |
| **Proto Definitions** | [github.com/BitBadges/bitbadgeschain/tree/master/proto](https://github.com/BitBadges/bitbadgeschain/tree/master/proto) |
| **Full Docs (.txt)** | [for-llms.txt](../for-llms.txt) |
| **SDK Reference** | [TypeDoc](https://bitbadges.github.io/bitbadgesjs/classes/BitBadgesAPI.html) — [Types JSON](https://github.com/BitBadges/bitbadgesjs/blob/main/packages/bitbadgesjs-sdk/type-map/typedoc-output.json) |

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

{% content-ref url="../x-badges/" %}
[x-badges](../x-badges/)
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

## Developing with AI

Below, we provide some resources that may be helpful for developing with AI. If there is anything else we can do to make development easier, let us know!

[https://github.com/BitBadges/bitbadges-quickstarter-ai](https://github.com/BitBadges/bitbadges-quickstarter-ai)

[https://docs.bitbadges.io/\~gitbook/mcp](https://docs.bitbadges.io/~gitbook/mcp) - GitBook Provided MCP with searchDocumentation tool

[Cosmos Msg Proto Definitions](https://github.com/BitBadges/bitbadgeschain/tree/master/proto)

[BitBadges API OpenAPI Spec](https://raw.githubusercontent.com/bitbadges/bitbadgesjs/main/packages/bitbadgesjs-sdk/openapi-hosted/openapi.json)

[Full Documentation .txt](../for-llms.txt)

[Full SDK Reference](https://bitbadges.github.io/bitbadgesjs/classes/BitBadgesAPI.html) - [Full Types Reference](https://github.com/BitBadges/bitbadgesjs/blob/main/packages/bitbadgesjs-sdk/type-map/typedoc-output.json)

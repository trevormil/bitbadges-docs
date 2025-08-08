# 🔨 Getting Started

BitBadges is proud to offer no-code / low-code flows for all our major services. Simply navigate to the Create tab in-site, and get started creating tokens, claims, address lists, explore listings, applications, and more!

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

> **Need help?** Join our Discord for support from the BitBadges team and community developers.
>
> **Need $BADGE credits?** Contact us on Discord - we offer subsidized credits for developers during beta!
>
> **No-Code / In-Site Solutions** Check out the [Create tab](https://bitbadges.io/create) or the [developer portal](https://bitbadges.io/developer) first to see what all is possible. Most of the time, you can just do everything with no code directly in-site! No need for any direct integration. Let us handle everything!
>
> Get creative. You can gate URLs, Discords, and integrate with many of your favorite tools without a single line of code!

## Explore First, Read Later

We strongly recommend, if you have not already, to explore the claim tester and other creation options in-site. Many of your questions should be answered by the interface and is much easier to understand than a bunch of long text here in this documentation. Just go explore and experiment first.

Most of your setup and management (and oftentimes all) will be done directly in-site via the developer portal or Create tab. Get started at [https://bitbadges.io/create](https://bitbadges.io/create).

## Tokens vs Badges

We use the terms tokens and badges interchangeably. For example, "badgeIds" refers to token IDs. In the past, our niche was primarily earned badges, and we are slowly trying to rollout rebrands to full tokens. However, the core interface is still named "badges" behind the scenes.

## Gate Any Service In 2 Steps

Any service can be gated by ANY criteria simply in just 2 steps.

1. **Authenticate** - We recommend Sign In with BitBadges but it could be any approach
2. **Verify Claim Success** - Check the user satisfies a BitBadges claim (via post-success hooks, API, many ways!)

Claims are the universal connectors. Claim criteria can be anything from a payment to token ownership to Discord servers. Implement it any way you want (including custom logic) at [https://bitbadges.io/create](https://bitbadges.io/create)!

## Quick Start - Tokens

{% content-ref url="../x-badges/" %}
[x-badges](../x-badges/)
{% endcontent-ref %}

## Quick Start - Claims

This includes documentation everything from custom plugins to Zapier to dynamic stores and mroe

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
  ...YOUR_CONFIG_AND_API_KEY
});

await BitBadgesAPI.getCollection(...);
```

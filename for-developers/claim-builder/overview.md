# Overview

BitBadges Claims are designed to be a comprehensive tool directly within the site or via the API for you to customize the distribution criteria for your badges and address lists.

Out of the box, we offer plenty of built-in plugins and features with no code required and directly claimable by users in the site. Or, you can extend its functionality with helper tools, custom plugins, Zapier, our API, and more!

**When to use claims vs self-hosted balances?**

Claims are handled on a trigger basis. When something occurs or the user attempts to claim, a claim can be completed and transfer badges or perform another claim action.&#x20;

However, note claims may not be the right choice for you, especially if you already have all the data you need already. If you already have the data, you may consider self-hosting the balances / airdropping badges to your users. This removes the middle action step required to complete the process.

**What are plugins?**

Claims are made up of one or more plugins executed in order. For a claim to be successful, all plugins must pass.&#x20;

**How to create / manage claims?**

Claims are created and maintained through the BitBadges site. You will get the chance to create and update claims when creating / updating a badge collection or address list. The developer portal also has an interface for testing, creating, and managing claims. You can also use the [BitBadges API](../bitbadges-api/tutorials/) for more programmatic CRUD.

## **Completion Methods**

With BitBadges claims, you have a few approaches.

**Option 1: Directly In-Site via Plugins**

If everything you need is directly implementable with plugins, there is no extra development work needed. BitBadges has created some plugins, and custom plugins can also be created by developers.

See [https://bitbadges.io/plugin-directory](https://bitbadges.io/plugin-directory).

<figure><img src="../../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

**Option 2: Auto-Claim by Zapier**

You can can extend BitBadges Claims with our custom Zapier integration that allows you to connect claims with over 7000+ apps. See all supported apps here: [https://zapier.com/apps](https://zapier.com/apps).&#x20;

For example, make a purchase on Shopify -> get airdropped a badge or complete a course on Udemy -> get a completion badge. The "airdrop" is a claim completion.

<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

**Option 3: Auto-Claim by API**

You can implement a hybrid approach on your own using the BitBadges API and connect it behind the scenes. This allows you full flexibility over the claiming process.

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, { ... });
```

**Get Creative!**

A common theme you may see when implementing claims is to get creative and think outside the box. There are a ton of integrations and features already implemented. Even if your use case doesn't exactly match the implementation, you can get creative and implement workarounds.

For example,

-   Give out claim codes / passwords to those who meet the criteria on your end rather than needing a direct integration.
-   Many apps and services are email based rather than username based. Consider using the Email plugin universally.

## Claiming on Behalf of Others

With each claim, there is only one address (the claiming address). There is no initiator and recipient like with standard approvals. If you want to initiate a claim on behalf of others, you have to get a little creative.

**Password (Secrets) Approach**

The typical approach (used for Zapier and other examples) is to disable all user authentication (Signed In to BitBadges) but gate the claim with a password or other secret information. This allows you (who has knowledge of the password) to complete claims.

Because the authentication check is disabled, you can specify any claiming address. However, note that this disables the authentication check for everyone. You must gate the claim in other ways (like the password).

**OAuth - Complete Claim Scope**

Or, you could also be authorized to complete claims on behalf of the user via the BitBadges API, but this is only used in some cases. You will need separate authorizations from every claiming user.

## Distribution of Claim Information

If your users require secret information to be able to claim (e.g. unique claim codes via the Codes plugin), you can do this via any method you prefer since this information is non-crypto native (BitBadges in-site claim alerts, email, SMS, etc). You know your users best!

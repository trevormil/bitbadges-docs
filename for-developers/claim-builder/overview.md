# Overview

BitBadges Claims are designed to be a comprehensive tool directly within the site or via the API for you to customize the experience for you and your users. It allows you to easily establish the requirements for claiming your badges or being added to your address lists. The end goal is for all use cases to be supported by the BitBadges Claims. Out of the box, it offers plenty of built-in plugins and features with no code required and directly claimable by users in the site.

**When to use claims vs self-hosted balances?**

Claims are handled on a trigger basis. When something occurs, a claim can be completed and transfer badges / perform another claim action. However, note claims may not be the right choice for you, especially if you already have all the data you need already. If you already have the data, you may consider self-hosting the balances / airdropping badges to your users. This removes the middle action step required to complete the process.

**Approaches**

With BitBadges claims, you have a few approaches.

**Option 1: Directly In-Site via Plugins**

If everything you need is directly implementable with plugins, there is no extra dev work needed. BitBadges offers some core plugins, and custom plugins can also be created by developers.

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

**Option 2: Auto-Claim by Zapier**

You can can extend BitBadges Claims with our custom Zapier integration that allows you to connect claims with over 6000+ apps. See all supported apps here: [https://zapier.com/apps](https://zapier.com/apps). For example, make a purchase on Shopify -> get airdropped a badge or complete a course on Udemy -> get a completion badge. The "airdrop" is a claim completion.

<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

**Option 3: Custom Claims**

You can implement a hybrid approach in your own using the BitBadges API and connect it behind the scenes. This allows you full flexibility over the claiming process.

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, { ...body });
```

## Claiming on Behalf of Others

With each claim, there is only one address (the claiming address). There is no initiator and recipient like with standard approvals. If you want to initiate a claim on behalf of others, you have to get a little creative.

**Password (Secrets) Approach**

The typical approach (used for Zapier and other examples) is to disable all user authentication (Signed In to BitBadges) but gate the claim with a password or other secret information. This allows you (who has knowledge of the password) to complete claims.

Because the authentication check is disabled, you can specify any claiming address. However, note that this disables the authentication check for everyone. You must gate the claim in other ways (like the password).

**OAuth - Complete Claim Scope**

Or, you could also be authorized to complete claims on behalf of the user via the BitBadges API, but this is only used in rare cases. You will need separate authorizations from every claiming user.

## Distribution of Claim Information

If your users require secret information to be able to claim (e.g. unique claim codes via the Codes plugin), you can do this via any method you prefer since this information is non-crypto native (BitBadges in-site claim alerts, email, SMS, etc). You know your users best!

## **Plugins**

The BitBadges claim builder adopts a plugin-based approach to allow for maximum customization and integrations. See all plugins and helper tools via the directory. Mix and match plugins as you wish. ALL plugins for a claim need to pass for a successful claim.

{% embed url="https://bitbadges.io/claims/directory" %}

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

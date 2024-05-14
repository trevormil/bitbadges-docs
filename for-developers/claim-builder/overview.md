# Overview

BitBadges Claims are designed to be a comprehensive tool directly within the site or via the API for you to customize the experience for you and your users. It allows you to easily establish the requirements for claiming your badges or being added to your address lists.&#x20;

The end goal is for all use cases to be supported by the BitBadges Claims. Out of the box, it offers plenty of built-in plugins and features with no code required and directly claimable by users in the site.

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

Or, you can can extend it with our custom Zapier integration that allows you to connect claims with over 6000+ apps. See all supported apps here: [https://zapier.com/apps](https://zapier.com/apps).

For example, make a purchase on Shopify -> get airdropped a badge or complete a course on Udemy -> get a completion badge.

<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

Or, you can implement a hybrid approach with your own stuff using the BitBadges API and connect it behind the scenes.

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, { ...body });
```

## Distribution of Claim Information

If your users require secret information to be able to claim (e.g. unique claim codes via the Codes plugin), you can do this via any method you prefer since this information is non-crypto native (BitBadges in-site claim alerts, email, SMS, etc). You know your users best!

## **Plugins**

The BitBadges claim builder adopts a plugin-based approach to allow for maximum customization and integrations. See all plugins and helper tools via the directory. Mix and match plugins as you wish. ALL plugins for a claim need to pass for a successful claim.

{% embed url="https://bitbadges.io/claims/directory" %}

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

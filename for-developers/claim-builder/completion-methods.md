# Completion Methods

With BitBadges claims, you will have a couple ways of completing claims. Note on-demand do not have a completion "action", so this is only applicable to standard claims.

**Option 1: Directly In-Site (Recommended)**

Users can claim directly on the corresponding page directly in the BitBadges site. We envision this is to be used for almost all cases. Custom logic can be implemented through custom plugins or webhooks or other means.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Option 2: Auto-Claim by Zapier**

You can can extend BitBadges Claims with our custom Zapier integration that allows you to connect claims with over 7000+ apps. See all supported apps here: [https://zapier.com/apps](https://zapier.com/apps).

For example, make a purchase on Shopify -> get allocated a badge or complete a course on Udemy -> get a completion badge. The "airdrop" is a claim completion.

<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

Note: Zapier can also be used to automate other parts of the claim, like specific plugins or implementing post-claim rewards.

**Option 3: Auto-Claim by API**

You can implement a hybrid approach on your own using the BitBadges API and connect it behind the scenes. This allows you full flexibility over the claiming process.

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, { ... });
```

**Get Creative!**

A common theme you may see when implementing claims is to get creative and think outside the box. There are a ton of integrations and features already implemented. Even if your use case doesn't exactly match the implementation, you can get creative and implement workarounds.

For example,

* Give out claim codes / passwords to those who meet the criteria on your end rather than needing a direct integration.
* Many apps and services are email based rather than username based. Consider using the Email plugin universally.

## Claiming on Behalf of Others

With each claim, there is only one address (the claiming address). There is no initiator and recipient like with standard approvals. If you want to initiate a claim on behalf of others, you have to get a little creative.

**Password (Secrets) Approach**

The typical approach (used for Zapier and other examples) is to disable all user authentication (Signed In to BitBadges) but gate the claim with a password or other secret information. This allows you (who has knowledge of the password) to complete claims.

Because the authentication check is disabled, you can specify any claiming address. However, note that this disables the authentication check for everyone. You must gate the claim in other ways (like the password).

<figure><img src="../../.gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>

**OAuth - Complete Claim Scope**

Or, you could also be authorized to complete claims on behalf of the user via the BitBadges API, but this is only used in some cases. You will need separate authorizations from every claiming user. See Sign In with BitBadges for how to implement.

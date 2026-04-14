# Designing Claims

This page walks through common approaches for designing claims — how to gate criteria, distribute rewards, and integrate with external systems. Claims are very flexible and versatile. There are many ways to achieve your desired outcome.

## Think About Your Flow

Every claim boils down to three questions:

1. **How do you identify the user?** (address, etc)
2. **How do you verify the user meets the criteria?** (codes, address lists, token ownership, custom logic, etc.)
3. **What happens when they succeed?** (mint a token, unlock content, grant access, trigger an external action)

And with the in-built plugins, universal claim codes, dynamic stores potentially controlled by you, or custom plugins, you can gate anything and implement any rewards.

## Gating Criteria

### Claim Codes

Generate codes and distribute them however you want — email, SMS, in-person, QR codes. Users redeem codes in-site. No integrations required.

```typescript
import CryptoJS from 'crypto-js';

// Generate deterministic codes from a seed
const generateCodesFromSeed = (
    seedCode: string,
    numCodes: number,
): string[] => {
    const codes = [];
    for (let i = 0; i < numCodes; i++) {
        const hash = CryptoJS.SHA256(`${seedCode}-${i}`).toString();
        codes.push(`${hash}-${i}`);
    }
    return codes;
};
```

Codes are one-time use by default. You can also use a shared **password** for simpler cases where uniqueness isn't needed. These are registered through the `codes` plugin (or `password` for passwords).

### Address Whitelists

Gate claims to a known set of addresses. Use the `whitelist` plugin with either a static address list or a dynamic store.

### Dynamic Stores

Serverless address lists hosted by BitBadges. You add and remove addresses via API calls — from your backend, a Zapier automation, an LLM agent, a cron job, or any system that can make HTTP requests. Attach a store to a claim, and only users in the store can claim. The store updates independently of the claim configuration, so you can change eligibility in real-time without reconfiguring the claim itself. See [Dynamic Stores](dynamic-stores.md) for full details.

```typescript
// Add a user to a store via API — triggered from anywhere
await BitBadgesApi.performStoreAction({
    dynamicDataId: 'eligible-users',
    dataSecret: 'your-store-secret',
    actionName: 'add',
    payload: { address: 'bb1...' }
});
```

### Token Ownership

Use the `must-own-badges` or `min-badge` plugins to require on-chain token ownership. No external setup needed — these check balances directly.

### Custom Plugins

For any criteria not covered by built-in plugins, create a custom plugin in the [developer portal](https://bitbadges.io/developer). Your HTTP endpoint receives the user's address and returns success/failure. See [Custom Plugins](custom-plugins.md) for implementation details.

## Rewards & Utility

### Gated Content / URLs

Use the in-site rewards tab to link content or URLs that become visible only after a successful claim. For additional security, combine with Sign In with BitBadges for authenticated redirect support.

### Success Webhooks

Create custom plugins to trigger external actions when a claim succeeds. This can be used to send an email, call an API, update a database, trigger a webhook, trigger an AI agent, or any other action you need.

### On-Chain Token Mints (Gating Approvals)

Claims can gate on-chain approvals (mints), giving users a permanent, verifiable on-chain credential. See [Gating On-Chain Approvals](gating-approvals.md) for the full hybrid process, consistency requirements, and trust assumptions.

### External Actions

Trigger any external action from your custom plugin endpoint — send an email, call an API, update a database, trigger a webhook. Your plugin runs during claim processing, so you can execute side effects on success.

### Points & Rewards Systems

Claims integrate with BitBadges' points system. Award points on successful claims and use point balances as criteria for other claims or access control.

## Leveraging Automation

### AI-Powered Criteria

With AI x Builder x LLMs and services like Zapier or Pipedream, you really have endless possibilities for checking criteria and implementing rewards on your own. It is all just one prompt away.

**Criteria side:** Pre-evaluate users with your AI model and add eligible addresses to a dynamic store. This avoids long wait times during claim execution and provides better UX.

```typescript
// AI model evaluates eligibility, adds to store
const eligible = await yourAIModel.evaluate(userAddress);
if (eligible) {
    await BitBadgesApi.performStoreAction({
        dynamicDataId: 'ai-eligible',
        dataSecret: process.env.STORE_SECRET,
        actionName: 'add',
        payload: { address: userAddress }
    });
}
```

**Reward side:** Create a custom plugin whose endpoint triggers your AI model or any downstream service upon claim processing.

### Webhooks & Integrations

Some services can check claims natively (e.g., WordPress gated sites just need the claim ID). For everything else, use the [Claims API](api-reference.md) to verify claim success programmatically:

```typescript
const res = await BitBadgesApi.checkClaimSuccess(claimId, userAddress);
if (res.successCount >= 1) {
    // User has claimed — grant access
}
```

## Combining Approaches

Claims support configurable success logic (AND/OR/NOT). This lets you combine multiple approaches:

- **AND:** User must own a badge AND enter a valid code
- **OR:** User can claim with a code OR by being on the whitelist
- **M-of-N:** User must satisfy at least 2 of 5 plugins

See [Success Logic](concepts.md#success-logic) for the full `iSatisfyMethod` interface.

## Design Tips

- **Start simple.** Use built-in plugins before building custom ones. Codes and passwords solve most distribution problems. Serverless dynamic stores offer more flexibility and are less complex to manage than full custom plugin endpoints.
- **Decouple criteria from claims.** Dynamic stores let you change eligibility without reconfiguring the claim.
- **Use on-chain tokens for permanence or a reusable primitive.** If you need criteria that persists and is verifiable by third parties, mint a non-transferable badge as the claim reward. You can also then use this as proof of criteria in on-chain contexts.
- **Gate with passwords for auto-completion.** Any time your backend completes claims on behalf of users, disable sign-in and use a password only your backend knows. Claim on behalf of addresses without requiring them to sign in.
- **Test with simulations.** Use `simulateClaim()` or the in-site claim tester before going live.

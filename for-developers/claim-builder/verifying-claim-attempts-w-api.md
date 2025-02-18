# Verifying Claim Attempts w/ API

A big part of offering gated utility is to actually check if the user meets the criteria or not. While claims and BitBadges allows you to outsource all the heavy authentication logic to us, you still need to implement the connection logic and verify the attempt with the API if you are offering custom utility.

IMPORTANT: Verifying claim attempts are two-fold:

* Authentication: Verify the user owns the claiming address (can be done with Sign In with BitBadges)
* Verifying Claim Attempt: Lookup the claim attempt via the BitBadges API and cross-check the address satisfied criteria

{% content-ref url="../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../authenticating-with-bitbadges/)
{% endcontent-ref %}

### Flexible Option 1: Check Claim Success

The recommended way to check if a user has claimed or not is simply with the checkClaimSuccess router. This is compatible with both standard and on-demand claims.

This simply returns a **successCount** with the number of times a user has completed the claim. For on-demand claims, the success count will be 1. With standard, it will be the number of times the user has claimed in the current state.

For more information, you can fetch specific claim attempts or parse state as needed.

```tsx
const res = await BitBadgesApi.checkClaimSuccess(claimId, chain.bitbadgesAddress ?? '');
setAlreadyClaimed(res.successCount > 0);
```

### Standard Only Option 1: Get Claim Attempts

The easiest way to get claim attempts is to use the `getClaimAttempts` method. This will return a paginated list of claim attempts for a specific address and claim ID.

```ts
import { BitBadgesApi } from 'bitbadgesjs-sdk';

const api = new BitBadgesApi({
    // Your config
});

// GET /api/v0/claims/:claimId/attempts
let prevBookmark = '';
const claimAttemptsResponse = await api.getClaimAttempts(claimId, {
    address,
    bookmark: prevBookmark,
});
const { docs, bookmark, total } = claimAttemptsResponse;
const claimAttempts = docs;

//Ex: Check minimum one success
const success = claimAttempts.some((attempt) => attempt.success);
```

### Standard Only Option 2: Parse State

You can also parse the state of the claim to get more information. This is useful if you want to check specific plugin state values (maybe email or some other). Note that certain state is private and only accessible via authenticated requests and when requested. You can see an example JSON of a specific claim in-site with the info circle button -> JSON tab.

```ts
const claimsRes = await BitBadgesApi.getClaims({
    claimIds: [claimId],
    fetchPrivateParams: false, // True to return private params (must have permissions to view)
    fetchAllClaimedUsers: true, // True to return all claimed users for the claim (in the numUses plugin publicState)
    privateStatesToFetch: [{ claimId, instanceId }] // If you need private state (certain state is public)
});
const claim = claimsRes.claims[0];
const bitbadgesAddress = convertToBitBadgesAddress(...);
const numUsesPlugin = claim.plugins.find((plugin) => plugin.pluginId === 'numUses');
const claimNumbers = numUsesPlugin?.publicState.claimedUsers[bitbadgesAddress];
// claimNumbers is a 0-based claim numbers array (e.g. [0, 3, 5])

//TODO: Customize as needed
if (claimNumbers.length >= 1) {
    //User has successfully claimed at least once
}
```

### On-Demand Option 1: Simulations

With on-demand claims, there are no claim attempts or state, so the above approaches do not work.

For these, you can use the simulate claim endpoint. Simulations are treated the same as checking the criteria for on-demand. See the next page for how to actually "complete" or "simulate" the on-demand claim.

```typescript
await BitBadgesApi.simulateClaim(...)
```

{% content-ref url="auto-completing-claims/auto-complete-claims-w-bitbadges-api.md" %}
[auto-complete-claims-w-bitbadges-api.md](auto-completing-claims/auto-complete-claims-w-bitbadges-api.md)
{% endcontent-ref %}

### More Advanced Implementations

Depending on your implementation, you may already have what you need. You can also setup custom plugins, custom success webhooks, or custom forms (we store more detailed request information for you) for more advanced implementations. We refer you to the corresponding plugin and our documentation for more info.

# Verifying Claim Attempts w/ API

A big part of offering gated utility is to actually check if the user meets the criteria or not. While claims and BitBadges allows you to outsource all the heavy authentication logic to us, you still need to implement the connection logic and verify the attempt with the API if you are offering custom utility.

IMPORTANT: Verifying claim attempts are two-fold:

* Authentication: Verify the user owns the claiming address (can be done with Sign In with BitBadges)
* Verifying Claim Attempt: Lookup the claim attempt via the BitBadges API and cross-check the address satisfied criteria

{% content-ref url="../../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../../authenticating-with-bitbadges/)
{% endcontent-ref %}

## Options

### Option 1: Get Claim Attempts

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

### Option 2: Parse State

You can also parse the state of the claim to get more information. This is useful if you want to check specific plugin state values (maybe email or some other). Note that certain state is private and only accessible via authenticated requests and when requested. You can see an example JSON of a specific claim in-site with the info circle button -> JSON tab.

```ts
const claimsRes = await BitBadgesApi.getClaims({
    claimIds: [claimId],
    includePrivateParams: false // True to return private params (must have permissions to view)
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

### Option 3: More Advanced Implementations

Depending on your implementation, you may already have what you need. You can also setup custom plugins, custom success webhooks, or custom forms (we store more detailed request information for you) for more advanced implementations. We refer you to the corresponding plugin and our documentation for more info.

# Fetching Claims

More documentation is available via the API routes for fetching claims.&#x20;

Go to the JSON tab of the claim details in-site to see an example one. Note that fetchPrivateParams: true is necessary to get certain private information. You must have permissions to view private parameters.

```typescript
const claimsRes = await BitBadgesApi.getClaims({
    claimsToFetch: [{ claimId, fetchPrivateParams: false, fetchAllClaimedUsers: true, privateStatesToFetch: [instanceId] }],
});
const claim = claimsRes.claims[0];
const bitbadgesAddress = convertToBitBadgesAddress(...);
const numUsesPlugin = claim.plugins.find((plugin) => plugin.pluginId === 'numUses');
const allClaimedUsers = Object.keys(numUsesPlugin.publicState.claimedUsers);
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

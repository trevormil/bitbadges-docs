# Fetching Claims

More documentation is available via the API routes for fetching claims.

Go to the JSON tab of the claim details in-site to see an example one. Note that fetchPrivateParams: true is necessary to get certain private information. You must have permissions to view private parameters.

```typescript
// GET https://api.bitbadges.io/api/v0/claim/claimId?...
const claimsRes = await BitBadgesApi.getClaim({ 
    claimId, 
    fetchPrivateParams: false, 
    fetchAllClaimedUsers: true, 
    privateStatesToFetch: [instanceId],
});
```

<figure><img src="../../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

# Fetching Claims

More documentation is available via the API routes for fetching claims. Go to the JSON tab of the claim details in-site to see an example one. Note that includePrivateParams: true is necessary to get certain private information. You must have permissions to view private parameters.&#x20;

{% content-ref url="../../bitbadges-api/tutorials/getting-claims.md" %}
[getting-claims.md](../../bitbadges-api/tutorials/getting-claims.md)
{% endcontent-ref %}

```typescript
const claimsRes = await BitBadgesApi.getClaims({
    claimIds: [claimId],
    includePrivateParams: false // True to return private params (must have permissions to view)
});
const claim = claimsRes.claims[0];
const bitbadgesAddress = convertToBitBadgesAddress(...);
const numUsesPlugin = claim.plugins.find((plugin) => plugin.pluginId === 'numUses');
```

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

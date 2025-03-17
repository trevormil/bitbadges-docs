# Overview

This section is a knowledge dump for more advanced use cases and how badges operate behind the scenes.  For most use cases, you will not care about any of this as it will be handled for you via the site. And if you are self-implementing a badge-gated service, you can just fetch badge balances and metadata from the API without worrying about the underlying details.

```typescript
const res = await BitBadgesApi.getBadgeBalanceByAddress(collectionId, address, { ...options });
console.log(res);

const res = await BitBadgesApi.getBadgeMetadata(1, 5);
```

{% content-ref url="../bitbadges-api/" %}
[bitbadges-api](../bitbadges-api/)
{% endcontent-ref %}

This section will go into more advanced details like the badge interface, how it operates via the blockchain, how approvals are implemented, etc.

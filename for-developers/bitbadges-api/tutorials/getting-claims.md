# Getting Claims

If you know the claim ID:

```typescript
import { BitBadgesApi } from 'bitbadges-api';

const getClaims = async () => {
  try {
    const res = await BitBadgesApi.getClaims({ 
      claimIds: ['claimId1', 'claimId2', 'claimId3'],
      listId: '...' // only required if claim is for a private list and you are not claim creator
    });
    
    const { claims } = res;
    console.log(claims);
  } catch (error) {
    console.error('Error fetching claims:', error);
  }
};

getClaims();
```

You can also get claims via the **claims** field on the returned values for collections and address lists.

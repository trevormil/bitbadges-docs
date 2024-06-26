# Getting Claims

If you know the claim ID:

```typescript
import { BitBadgesApi } from 'bitbadges-api';

const getClaims = async () => {
  try {
    const res = await BitBadgesApi.getClaims({ 
      claimIds: ['claimId1', 'claimId2', 'claimId3'],
      listId: 'optionalListId' // only required if claim is for a private list
    });
    
    const { claims } = res;
    console.log(claims);
  } catch (error) {
    console.error('Error fetching claims:', error);
  }
};

getClaims();
```

Or, you can get it via the **claims** field on the returned values for collections and address lists.

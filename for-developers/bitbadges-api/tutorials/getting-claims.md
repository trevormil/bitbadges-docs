# Getting Claims

If you know the claim ID:

```typescript
import { BitBadgesApi } from 'bitbadges-api';

const getClaims = async () => {
    try {
        const res = await BitBadgesApi.getClaims({
            claimsToFetch: [
                { claimId: 'claimId1', fetchAllClaimedUsers: true },
                { claimId: 'claimId2' },
                { claimId: 'claimId3' },
            ],
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

**Fetching Claimees**

First, assume you have a claim object that contains the claim information. Then do the following,

* Look for a plugin of type 'numUses' in the claim's plugins array.
* If found, access the publicState.claimedUsers object.
* Find the entry for the user's BitBadges address.
* Convert the resulting data into an array of claim numbers (zero-based).

Again, its important to note that the claim numbers are zero-based. CLaim #1 (as maybe seen on the UI) is actually claim number 0 behind the scenes and so on.

Here's a code example demonstrating this process:

```javascript
function getClaimeeNumbers(claim, userBitBadgesAddress) {
    // Find the 'numUses' plugin
    const numUsesPlugin = claim.plugins.find(
        (plugin) => plugin.type === 'numUses'
    );

    if (!numUsesPlugin) {
        return []; // No 'numUses' plugin found
    }

    // Get the claimed users for the given address
    const claimedUsers =
        numUsesPlugin.publicState.claimedUsers[userBitBadgesAddress];

    if (!claimedUsers) {
        return []; // No claims found for this user
    }

    return claimNumbers; //Ex: [0, 5, 10]
}

// Usage example
const claim = {
    // ... other claim properties ...
    plugins: [
        {
            type: 'numUses',
            publicState: {
                claimedUsers: {
                    'bb1abc...': [0, 5, 10],
                },
            },
        },
    ],
};

const userBitBadgesAddress = 'bb1abc...';
const claimeeNumbers = getClaimeeNumbers(claim, userBitBadgesAddress);
console.log(claimeeNumbers); // Output: [0, 2, 4]
```

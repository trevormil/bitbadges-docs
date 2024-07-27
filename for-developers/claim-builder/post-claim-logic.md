# Post-Claim Logic

You may consider completing some additional action upon the user claiming successfully.

**Webhooks Plugin**

Consider using the in-site webhooks plugin which will send a request upon every claim attempt. It is important to note that this may send on FAILED attempts and simulations as well (or other parties could send spoofed requests). This is because it is an actual plugin and part of the validation process. Thus, it might send a webhook but fail a later plugin (resulting in the claim overall failing). This also means it will trigger during simulations as well, so you need to filter.

To handle this, you must double check that the attempt was successful by verifying it via its attempt ID.

```typescript
// At your webhook handler POST URL

const body = req.body;
/*
{
  cosmosAddress: 'cosmos1zd5dsage58jfrgmsu377pk6w0q5zhc67fn4gsl',
  claimId: '7a5b61ce3f2cf5eeb89f453c87f2b970328356395bf4ac4a23702d2fb0fb63c9',
  _isSimulation: false,
  lastUpdated: 1722088273192,
  createdAt: 1722088273192,
  claimAttemptId: '',
  isClaimNumberAssigner: false,
  maxUses: 1,
  currUses: 0,
  instanceId: 'e44ba88643381cd5fa09be288490a92c64add8bcd2327d29a11a4227fab55e5e',
  pluginId: 'webhooks'
}
*/

if (body._isSimulation) return;

const claimAtemptId = body.claimAttemptId;
const status = await BitBadgesApi.getClaimAttemptStatus(claimAttemptId);

// Handle 
```

**Webhooks by Zapier**

Consider using the Webhooks by Zapier plugin as an additional action to the flow.

**BitBadges API**

If you already are auto-claiming with the BitBadges API, you already know when users claim, so you can just implement whatever you need right there.

Or, you can poll the GET claims endpoint to detect when new users have successfully claimed.

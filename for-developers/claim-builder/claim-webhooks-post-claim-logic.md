# Claim Webhooks (Post-Claim Logic)

Need to perform some additional action upon the user claiming successfully?

**Custom Plugins as Webhooks**

Consider creating a custom plugin and using it as a webhook. It does not even need to have any logic, just return a 200 OK.&#x20;

Note that since it is a plugin though, it will send upon every attempt (even if failed or a simulated attempt). Thus, it is important you verify that the attempt was successful via the ID. Also, you may have to sleep a couple seconds for processing.

```typescript
// At your webhook plugin handler URL

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
  pluginId: '...'
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

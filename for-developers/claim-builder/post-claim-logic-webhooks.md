# Post-Claim Actions / Webhooks

Need to perform some additional action upon the user claiming successfully? There are two ways you can wait until success.

1. You will receive the claim attempt ID within the context. You can then poll with the API to check its status. This may take a couple seconds for processing.
2. If creating a custom plugin, you can subscribe to status webhooks. We will send a duplicate request post-completion with attemptStatus='success'. Note this sends to the same handler, same URI, same method, so you have to catch the attemptStatus accordingly.
   1. You should send a 200 OK (or however your plugin is configured) during execution time to allow the claim to succeed.

**Custom Plugins as Webhooks**

Consider using a custom plugin or the Custom Validation URL plugin as a webhook that is triggered for each claim attempt.&#x20;

```typescript
// At your plugin handler URL

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
  _attemptStatus: 'executing', // or 'success'
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

Consider using the Webhooks by Zapier plugin on Zapier as an additional action to your Zap flow.

**BitBadges API**

If you already are auto-claiming with the BitBadges API, you already know when users claim, so you can just implement whatever you need right there.

Or, you can poll the GET claims endpoint to detect when new users have successfully claimed.

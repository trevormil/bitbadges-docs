# Post-Claim Actions / Webhooks

Need to perform some additional action upon the user claiming successfully?

**Custom Plugins as Webhooks**

Consider using a custom plugin or the Custom Validation URL plugin as a webhook that is triggered for each claim attempt.&#x20;

Note that since it is a plugin within the claim:

* It will send upon every attempt (even if failed or a simulated attempt). If you need to verify success status,  you can verify attempt was successful via the passed ID. This will require an asynchronous approach. You will need to return 200 OK at claim attempt time (in order for the claim to succeed). Think of this like a "received webhook successfully" response. Then, you can wait a couple seconds for processing, and then execute the action / check status.
* Some post-claim actions may be able to fail without affecting the claim status. However, all plugins in the claim must pass to successfully claim. Workaround this by sending a 200 OK back to BitBadges from the plugin at claim attempt time, acknowledging the webhook was received. Then, you can execute the action after the fact (potentially failing).&#x20;

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

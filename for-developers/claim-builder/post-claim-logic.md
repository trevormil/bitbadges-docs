# Post-Claim Logic

You may consider completing some additional action upon the user claiming successfully.

**Webhooks Plugin**

Consider using the in-site webhooks plugin which will send a request upon every claim attempt. It is important to note that this may send on FAILED attempts as well (or other parties could send spoofed requests).

You must double check that the attempt was successful by verifying it.

```typescript
// At your webhook handler POST URL

const claimAtemptId = req.body.claimAttemptId;
const status = await BitBadgesApi.getClaimAttemptStatus(claimAttemptId);

// Handle 
```

**Webhooks by Zapier**

Consider using the Webhooks by Zapier plugin as an additional action to the flow.

**BitBadges APi**

If you already are auto-claiming with the BitBadges API, you already know when users claim, so you can just implement whatever you need right there.

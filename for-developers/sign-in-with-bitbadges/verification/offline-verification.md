# Offline Verification

If you need to pre-fetch all (code, request) pairs before verification time (e.g. you are verifying in an offline setting), you can fetch all codes created via BitBadges for your client ID with the API.&#x20; This just returns the authorization codes, and you still need to exchange them (which is a one-time only process).

```typescript
const res = await BitBadgesApi.getSIWBBRequestsForDeveloperApp({
    clientId,
    bookmark: '',
});
console.log(res);
```

```typescript
await BitBadgesApi.verifySIWBBRequest({ ... });
await BitBadgesApi.exchangeSIWBBAuthorizationCode({ code: blockinChallenge._docId, options: { ... }});
```

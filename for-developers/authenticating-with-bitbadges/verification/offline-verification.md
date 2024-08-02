# Offline Verification

If you need to pre-fetch all (code, request) pairs before verification time (e.g. you are verifying in an offline setting), you can fetch all codes created via BitBadges for your client ID via the SDK below.&#x20;

This is a paginated request, so you will need to specify the bookmark received from the previous request. This does not fetch or include any requests with redirect URIs (only ones stored in users' accounts via the manual approach).

```typescript
const res = await BitBadgesApi.getSIWBBRequestsForDeveloperApp({
    clientId,
    bookmark: '',
});
console.log(res.SIWBBRequests);
const blockinChallenge = res.SIWBBRequests[0];
```

Note that we do not provide verification responses by default. You will need to verify each individually. If you have time-dependent checks, note that by default, verification is done for the current time.

```typescript
await BitBadgesApi.verifySIWBBRequest({ ... });
await BitBadgesApi.exchangeSIWBBAuthorizationCode({ code: blockinChallenge._docId, options: { ... }});
```

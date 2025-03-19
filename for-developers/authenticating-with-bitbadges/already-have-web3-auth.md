# Already Have Web3 Auth?

If you already have Web3 authentication, then there is no need for the full Sign In with BitBadges step. Simply attach a claim verification step to your authentication process!

```typescript
// Pre-req: Create claim in BitBadges site
// 1. Authenticate your user (using your existing setup)
// 2. Verify claim success
const res = await BitBadgesApi.checkClaimSuccess(claimId, address);
```

Now, your service is gated by any criteria imaginable!

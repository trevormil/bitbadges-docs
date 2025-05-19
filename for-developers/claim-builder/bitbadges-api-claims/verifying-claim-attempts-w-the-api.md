# Verifying Claim Attempts w/ the API

IMPORTANT: Verifying claim attempts are two-fold:

* Authentication: Authenticate the user (can be done with Sign In with BitBadges or however)
* Verifying Claim Attempt: Lookup the claim attempt via the BitBadges API and cross-check the user has satisfied the criteria

Note: You may opt to simply receive a post-success webhook which would take the API verification step out of it.

```typescript
// Pre-Req: Set up your claim at https://bitbadges.io/create
// Pre-Req: User is authenticated

// 1. By address (if you already have it)
// GET https://api.bitbadges.io/api/v0/claims/success/{claimId}/{address}
// successCount will be 1 for on-demand claims and the number of completions for standard
const res = await BitBadgesApi.checkClaimSuccess(claimId, address);
if (res.successCount >= 1) { doSomething(); }

// 2. By attempt ID 
// GET https://api.bitbadges.io/api/v0/claims/status/{claimAttemptId}
const res = await BitBadgesApi.getClaimAttemptStatus(claimAttemptId);
if (res.success) { doSomething() }

// You may also browse all claim-based API routes in the reference like a fetch all claim
// attempts for a user, but the above two are typically what you are looking for.
```

**Claim Attempt IDs vs By Address**

If you already have the user address, you can simply use option 1.

If you want to verify by claim attempt ID, you can use option 2. Claim attempt IDs can be obtained if you are completing the claim on behalf of the user (e.g. via Zapier or the API), or you can set up a custom webhook to receive it.

Note: If you are receiving a post-success webhook, you already know the claim has gone through by the nature of it, so you do not need to verify it.

This will also allow you to map a user address / claim attempt to another social that you may identify your users by. For example, if you authenticate with email, you can request us to verify the user email, receive the email, and use the (ID, email) pair instead of an address.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use one of the plugins pictured below or a custom plugin to do so when setting up your claim.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**More Advanced Ways**

Note: You may also implement other ways of verifying claim attempts such as parsing state directly, storing data yourself from webhooks, etc. You can also trust the post-success webook to only be fired upon success. For these, we refer you to the corresponding documentation such as the API. The process is flexible, but the above should be all you need.

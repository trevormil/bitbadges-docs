# Verification Flow

From the prior pages, you should now have the authorization **code** (32 byte hex) for the user. This is either the QR code value for in-person or the redirect code you receive in your handler for the standard digital flow.

```typescript
// 32 byte hex string
async function myHandler(req: NextApiRequest, res: NextApiResponse) {
    const code = req.query.code as string;
}
```

You can now exchange the code and valid app configuration details. Be sure to keep the client secret secret. The response will contain all authentication details, including a **verificationResponse**.

**Exchanging the Code**

The next step is to exchange the code for authentication details. This is done via the exchange endpoint. This follows typical OAuth2 flow. You can also specify options to check the issued at time of code generation. Typically for in-person, you may need to disable the issued at time check of 10 minutes.

[API Reference](https://bitbadges.stoplight.io/docs/bitbadges) -> Exchange SIWBB Code

```typescript
// 32 byte hex string
async function myHandler(req: NextApiRequest, res: NextApiResponse) {
    const code = req.query.code as string;

    const options = {
        issuedAtTimeWindowMs: 1000 * 60 * 10, // 10 minutes (set to 0 to disable)
    };

    // POST https://api.bitbadges.io/api/v0/siwbb/token
    const res = await BitBadgesApi.exchangeSIWBBAuthorizationCode({
        code,
        options,
        grant_type: 'authorization_code',
        client_secret: '...',
        client_id: '...',
        redirect_uri: '...', //only needed for digital immediate flow
    });

    const { address, chain, verificationResponse } = res;
    if (!verificationResponse.success) {
        console.log(verificationResponse.errorMessage);
        throw new Error('Not authenticated');
    }
}
```

**Access Tokens / Session Management**

We return access tokens and refresh tokens for you to perform session management. The expiration times can help you implement sessions for your users, and you can health check the user hasn't revoked through the API. Code exchanges can only be performed once per unique code which is checked on our end.

```typescript
// 32 byte hex string
async function myHandler(req: NextApiRequest, res: NextApiResponse) {
    const code = req.query.code as string;

    // POST https://api.bitbadges.io/api/v0/siwbb/token
    const res = await BitBadgesApi.exchangeSIWBBAuthorizationCode({ ... });

    const { access_token, access_token_expires_at, refresh_token, refresh_token_expires_at } = res;
    // TODO: You can use these tokens for session management and authorized API access
}
```

Note this is not the only way of implementing sessions. You may implement custom approaches on your own like checking IDs, stamping hands, using claim numbers, etc.

{% content-ref url="api-access-tokens.md" %}
[api-access-tokens.md](api-access-tokens.md)
{% endcontent-ref %}

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Verifying Other "Attached" Criteria**

IMPORTANT: Note that the authorization code ONLY checks address verification. For any other criteria like claim verification, you must check this additionally server-side. Do not trust that if you added `claimId` in the URL params that it is automatically verified.

You must use the combination of 1) address verification via SIWBB and 2) your other checks like claims to ensure the user meets the criteria. Address verification is handled via SIWBB (the authorization code), but the other checks are a separate process that needs to be checked server-side.

Claims are an all inclusive way to build complex no-code criteria flows, but this can be anything you want. We refer you to the respective documentation for how to verify other criteria.

{% content-ref url="../../claim-builder/verifying-claim-attempts-w-api.md" %}
[verifying-claim-attempts-w-api.md](../../claim-builder/verifying-claim-attempts-w-api.md)
{% endcontent-ref %}

```typescript
async function myHandler(req: NextApiRequest, res: NextApiResponse) {
    const code = req.query.code as string;

    // Exchange code from above

    const { address, chain, verificationResponse } = res;

    // ...

    // After verifying the address, you can now check other criteria with knowledge that the user is the owner of the address
    // const claimAttemptsByAddress = await BitBadgesApi.getClaimAttempts(claimId, { address });
    // const ownershipRequirementsRes = await BitBadgesApi.verifyOwnershipRequirements(...);
    // const addressListsRes = await BitBadgesApi.getAddressLists(...);
}
```

**Putting It Together**

```typescript
import { BitBadgesApi } from 'bitbadgesjs-sdk';

// 32 byte hex string
async function myHandler(req: NextApiRequest, res: NextApiResponse) {
    const code = req.query.code as string;

    const res = await BitBadgesApi.exchangeSIWBBAuthorizationCode({
        code,
        options,
        grant_type: 'authorization_code',
        client_secret: '...',
        client_id: '...',
        redirect_uri: '...', //only needed if immediate
    });

    const { address, chain, verificationResponse } = res;
    if (!verificationResponse.success) {
        console.log(verificationResponse.errorMessage);
        throw new Error('Not authenticated');
    }

    const {
        access_token,
        access_token_expires_at,
        refresh_token,
        refresh_token_expires_at,
    } = res;
    // TODO: You can use these tokens for session management and authorized API access

    // After verifying the address, you can now check other criteria with knowledge that the user is the owner of the address
    // const claimsRes = await BitBadgesApi.getClaims({ claimIds: ['...'] });

    //TODO: Handle other checks and logic here
    // - Prevent replay attacks by checking timestamps or nonces
    // - Need to cache anything for later use?
    // - If verifying with assets, is the asset transferable and prone to flash ownership attacks (e.g. one use per asset, etc)?
    // - Other criteria needed for signing in? (e.g. whitelist / blacklist of addresses signing in)
    // - Verify claim criteria

    //TODO: If receiving attestations, are the contents valid?
    //For example:
    // - Verify the contents of the attestation messages are correct
    // - Verify the creator is who you expect
    // - Verify the metadata is correct
    // - Verify the on-chain anchors / update history are correct
    // - Verify the update history is correct
}
```

## **IMPORTANT: What is verified natively vs not?**

Does check :white\_check\_mark:

* Proof of address ownership via their authenticated BitBadges account
* Anything specified in the verify challenge options
* Issued at time of code generation is not too long ago if **options.isssuedAtTimeWindowMs** is specified. Defaults to 10 minutes.
* One exchange per authorization code -> access / refresh token

Does not check natively :x:

* Additional app-specific criteria needed for signing in (claims, owenrship requirements)
* Does not natively prevent against flash ownership attacks, replay attacks, or man-in-the-middle attacks other than what OAuth2 protects against

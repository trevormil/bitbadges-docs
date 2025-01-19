# Verification Flow

From the prior pages, you should now have the authorization **code** (32 byte hex) for the user. This is either the QR code value for in-person or the redirect code you receive in your handler for the standard digital flow.

You can now fetch the authentication details for the user, by exchanging the code and valid app configuration details. Be sure to keep the client secret secret. The response will contain all authentication details, including a **verificationResponse**.

**Verifying Other "Attached" Criteria**

IMPORTANT: Note that the authorization code ONLY checks address verification. For any other criteria like claim verification, you must check this additionally server-side. Do not trust that if you added `claimId` in the URL params that it is automatically verified. It is not.

You must use the combination of 1) address verification via SIWBB and 2) your other checks like claims to ensure the user meets the criteria. Address verification is handled via SIWBB (the authorization code), but the other checks are a separate process that needs to be checked server-side.&#x20;

We refer you to the respective documentation for how to verify other "attached" criteria.

```typescript
export interface VerifySIWBBOptions {
    /** How recent the challenge must be in milliseconds. Defaults to 10 minutes. If 0, we will not check the time. */
    issuedAtTimeWindowMs?: number;
}
```

**Access Tokens / Session Management**

We return access tokens and refresh tokens for you to perform session management. See more on the following page. The expiration times can help you implement sessions for your users, and you can health check the user hasn't revoked through the API. Code exchanges can only be performed once per unique code which is checked on our end.

Note this is not the only way of implementing sessions. You may implement custom approaches on your own like checking IDs, stamping hands, using claim numbers, etc.

{% content-ref url="api-access-tokens.md" %}
[api-access-tokens.md](api-access-tokens.md)
{% endcontent-ref %}

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Verification Example**

<pre class="language-tsx"><code class="lang-tsx"><strong>import { BlockinChallenge, BigIntify, BitBadgesApi, AttestationsProof } from "bitbadgesjs-sdk";
</strong>

const options: VerifySIWBBOptions = { 
    // Make sure the code was issued recently (defaults to 10 minutes) 
    // Set to 0 for skipping this check
    issuedAtTimeWindowMs: 60 * 1000
}

const res = await BitBadgesApi.exchangeSIWBBAuthorizationCode({ 
    code, 
    options,
    grant_type: 'authorization_code',
    client_secret: '...',
    client_id: '...',
    redirect_uri: '...' //only needed if immediate
});

const { address, chain, verificationResponse } = res;
if (!verificationResponse.success) {
    console.log(verificationResponse.errorMessage);    
    throw new Error("Not authenticated");
}

// If you received an access token because you are accessing the API w/ scopes
const { access_token, access_token_expires_at, refresh_token, refresh_token_expires_at } = res;
// TODO: Store these in the users sessions for future use and access

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
</code></pre>

## **IMPORTANT: What is verified natively vs not?**

It is important to note that BitBadges only checks from a cryptographic standpoint and does not implement any application specific logic. We handle checking the user owns the address (via a signature), but any other criteria needs to additionally be checked server-side by you (e.g. ownership requirements, claim criteria, etc). It is also critical that you prevent replay attacks, man-in-the-middle attacks, and flash ownership attacks (if verifying with assets) with best practices.

Does check :white\_check\_mark:

* Proof of address ownership via their authenticated BitBadges account
* Anything specified in the verify challenge options
* Issued at is not too long ago if **options.isssuedAtTimeWindowMs** is specified. Defaults to 10 minutes.
* One exchange per authorization code -> access / refresh token

Does not check natively :x:

* Additional app-specific criteria needed for signing in (claims, owenrship requirements)
* Does not prevent against flash ownership attacks

# Verification

## Verification

From the prior pages, you should now have the **code** for the user. You can now fetch the authentication details for the user, by providing the code and valid app configuration details. Be sure to keep the client secret secret. The response will contain all authentication details, including a **verificationResponse**.

**Access Tokens vs None**

If you specified API authorization scopes to be able to access, we return access tokens and refresh tokens for you to perform this functionality. In other words, scopes.length > 0. Otherwise, we do not return anything, and you will just receive the user crypto address in the response. You will then handle everything else on your end (session handling, etc).

If no access tokens are to be needed, we will simply return the user address as the access token for comaptibility with tools (just so the access token is not blank).

{% content-ref url="access-tokens.md" %}
[access-tokens.md](access-tokens.md)
{% endcontent-ref %}

**Verification Example**

<pre class="language-tsx"><code class="lang-tsx"><strong>import { BlockinChallenge, BigIntify, BitBadgesApi, AttestationsProof } from "bitbadgesjs-sdk";
</strong>

const options: VerifySIWBBOptions = { 
    // Set expected values for potentially manipulated values
    ownershipRequirements,
    otherSignIns: [],
    // Make sure the request is valid within 
    issuedAtTimeWindowMs: 60 * 1000
}
const res = await BitBadgesApi.exchangeSIWBBAuthorizationCode({ 
    code, 
    options,
    grant_type: 'authorization_code',
    client_secret: '...',
    client_id: '...',
    redirect_uri: '...' //only needed if redirected
});

const { ownershipRequirements, address, chain, verificationResponse, otherSignIns, attestationsPresentations } = res;
if (!verificationResponse.success) {
    console.log(verificationResponse.errorMessage);    
    throw new Error("Not authenticated");
}

// If you received an access token because you are accessing the API w/ scopes
const { access_token, access_token_expires_at, refresh_token, refresh_token_expires_at } = res;
// TODO: Store these in the users sessions for future use and access

//TODO: Handle other checks and logic here
// - Prevent replay attacks by checking timestamps or nonces
// - Need to cache the signature and message for later use?
// - If verifying with assets, is the asset transferable and prone to flash ownership attacks (e.g. one use per asset, etc)?
// - Other criteria needed for signing in? (e.g. whitelist / blacklist of addresses signing in)

//TODO: If using attestations proofs, are the contents valid? Above, we verified them to be well-formed from a cryptographic perspective, but you need to check the contents to ensure the proof is valid according to your application's rules.
//For example:
// - Verify the contents of the attestation messages are correct
// - Verify the creator is who you expect
// - Verify the metadata is correct
// - Verify the on-chain anchors / update history are correct
// - Verify the update history is correct
</code></pre>

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

**Verification - Manual**

Behind the scenes, the verification uses the equivalent of the following endpoint.

```typescript
await BitBadgesApi.verifySIWBBRequest({ ... });
```

**Pre-Fetching All Codes**

If you need to pre-fetch all codes before verification time (e.g. you are verifying in an offline setting), you can fetch all codes created via BitBadges for your client ID via the SDK below. Note that this

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

##

## **IMPORTANT: What is verified vs not?**

It is important to note that calling any verification function only checks from a cryptographic standpoint and does not implement any application specific logic. We handle checking the user's signature and verifying ownership of specified badges / attestations (if any). Any other custom requirements need to be handled by you separately (e.g. stamping users hands, checking IDs, etc.). It is also critical that you prevent replay attacks, man-in-the-middle attacks, and flash ownership attacks (if verifying with assets) with best practices.

Does check :white\_check\_mark:

* Proof of address ownership via their authenticated BitBadges account
* Asset ownership criteria is met for the address (if requested via **options.ownershipRequirements**). Its important to note that the ownershipRequirements specified in the URL / client-side is just for display purposes. You must request / check it server-side.
* Any options specified in the verify challenge options
* Attestations (if applicable) are well-formed from a cryptographic standpoint (data integrity, signed correctly) by the issuer. In other words, **attestation.createdBy** issued the credential, and it is valid according to the BitBadges expected format.
* Issued at is not too long ago if **options.isssuedAtTimeWindowMs** is specified.

Does not check :x:

* Additional app-specific criteria needed for signing in
* Any stateful data (e.g. handling sessions or checking nonces or preventing replay attacks or phishing attacks or flash ownership attacks)
* Does not handle sessions or check any session information
* Does not check the content of the attestation messages or anything else about the attestations besides that they are well-formed cryptographically.
  * Does not check if **attestation.createdBy** is the expected issuer (we check that they validly issued the attestation with correct signatures, but only you know who this is supposed to be).
* If requesting **otherSignIns,** you should verify that you receive a response (username / ID) for the requested sign-ins and not trust the response blindly. This is a client-side parameter so could potentially be tampered with maliciously. BitBadges verifies requests as-is, so a manipulated request will get a manipulated verification.
  * ```typescript
    for (const social of ['discord', 'twitter', 'github', 'google']) {
      if (!resp.otherSignIns?.[social]) {
        throw new Error('Invalid other sign in. Does not match expected.');
      }
    }
    ```



**Flash Ownership Attacks**

If authenticating with assets, you should be aware of flash ownership attacks. Basically, two sign ins at different times would be approved if the badge is transferred between the time of the first sign in and the second one. You may have to implement a one use per badge approach. Or, you can make the badges non-transferable during the time period of sign ins. See [here](https://blockin.gitbook.io/blockin/developer-docs/core-concepts) for more information.

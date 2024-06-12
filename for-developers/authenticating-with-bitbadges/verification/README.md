# Verification

## Verification

From the prior pages, you should now have the **code** for the user. You can now fetch the authentication details for the user, by providing the code and valid app configuration details. Be sure to keep **clientSecret** secret.

The response will contain all authentication details, including a **verificationResponse**. Note that the signature may be blank if you selected **allowReuseOfBitBadgesSignIn.**

<pre class="language-tsx"><code class="lang-tsx"><strong>import { BlockinChallenge, BigIntify, BitBadgesApi, AttestationsProof } from "bitbadgesjs-sdk";
</strong>

const options: VerifyChallengeOptions = { ... }
const res = await BitBadgesApi.getAndVerifySIWBBRequest({ 
    code, 
    options,
    clientSecret: '...',
    clientId: '...',
    redirectUri: '...' //only needed if redirected
});
const blockinChallenge = res.blockin;
const { ownershipRequirements, address, chain, verificationResponse, otherSignIns, attestationsPresentations } = blockinChallenge;
if (!verificationResponse.success) {
    console.log(verificationResponse.errorMessage);    
    throw new Error("Not authenticated");
}


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
await blockinChallenge.verify(api, ...);
await blockinChallenge.verifyOffline(...);
// or
await BitBadgesApi.verifySIWBBRequest({ ... });
await BitBadgesApi.getAndVerifySIWBBRequest({ code: blockinChallenge._docId, options: { ... }});
```

## **IMPORTANT: What is verified vs not?**

It is important to note that calling any verification function only checks from a cryptographic standpoint and does not implement any application specific logic. We handle checking the user's signature and verifying ownership of specified badges (if any). Any other custom requirements need to be handled by you separately (e.g. stamping users hands, checking IDs, etc.). It is also critical that you prevent replay attacks, man-in-the-middle attacks, and flash ownership attacks (if verifying with assets) with best practices.

As an authentication provider, you should NOT assume the returned details are correct. It is critical you verify the message is in the expected format when received from the user. There is no guarantee that the user (or BitBadges) did not manipulate the original message and sign a manipulated one. Blockin verifies the message as-is, so a manipulated message will get a manipulated verification response.&#x20;

Does check :white\_check\_mark:

* Proof of address ownership
* Asset ownership criteria is met for the address (if requested)
* Any options specified in the verify challenge options
* Attestations (if applicable) are well-formed from a cryptographic standpoint (data integrity, signed correctly) by the issuer. In other words, **attestation.createdBy** issued the credential, and it is valid according to the BitBadges expected format.

Does not check :x:

* Additional app-specific criteria needed for signing in
* Any stateful data (e.g. handling sessions or checking nonces or preventing replay attacks or phishing attacks or flash ownership attacks)
* Does not handle sessions or check any session information
* The ownership requirements content is not checked. You should assume the requirements were manipulated and check that the badges / assets are correct and match your desired auth details.
* Does not check if **attestation.createdBy** is the expected issuer (we check that they validly issued the attestation with correct signatures, but only you know who this is supposed to be).
* Does not check the content of the attestation messages or anything else about the attestations



**Flash Ownership Attacks**

If authenticating with assets, you should be aware of flash ownership attacks. Basically, two sign ins at different times would be approved if the badge is transferred between the time of the first sign in and the second one. You may have to implement a one use per badge approach. Or, you can make the badges non-transferable during the time period of sign ins. See [here](https://blockin.gitbook.io/blockin/developer-docs/core-concepts) for more information.

**Frontend vs Backend?**

Typically, your backend is the one that authenticates users and handles sessions, so verification is often done there. However, verifying on frontend vs backend is up to you and your application's requirements.

You can also consider a hybrid approach (e.g. check certain stuff and fail early on frontend). For example, you may do:

```tsx
//Frontend - Fails early if offline only checks fail
const isVerified = await blockinChallenge.verifyOffline(api, options); // Verify without assets. Does not require an API call.

//Backend - Full verification checking everything
const isVerified = await blockinChallenge.verify(api, options); // Verify with all options
```


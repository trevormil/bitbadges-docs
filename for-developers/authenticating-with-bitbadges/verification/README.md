# Verification

## Verification

From the prior pages, you should now have the **code** for the user. You can now fetch the authentication details for the user, by providing the code and valid app configuration details. Be sure to keep **clientSecret** secret.

The response will contain all authentication details, including a **verificationResponse**.

<pre class="language-tsx"><code class="lang-tsx"><strong>import { BlockinChallenge, BigIntify, BitBadgesApi, SecretsProof } from "bitbadgesjs-sdk";
</strong>

const options: VerifyChallengeOptions = { ... }
const res = await BitBadgesApi.getAuthCode({ 
    code, 
    options,
    clientSecret: '...',
    clientId: '...',
    redirectUri: '...' //only needed if redirected
});
const blockinChallenge = res.blockin;
const { params, address, chain, verificationResponse, publicKey, otherSignIns, message, signature, secretsPresentations } = blockinChallenge;
if (!verificationResponse.success) {
    console.log(verificationResponse.errorMessage);    
    throw new Error("Not authenticated");
}

// Alternative syntax: 
// const blockinChallenge = await BlockinChallenge.FromAuthCodeId(api, { code, options });
// const verificationResposne = blockinChallenge.verificationResponse
// ...

// If you want to verify with new options:
// const api = YOUR_API_INSTANCE
// await blockinChallenge.verify(api, { ... });
// console.log(blockinChallenge.verficationResponse)


//TODO: Handle other checks and logic here
// - Prevent replay attacks by checking the timestamp of the message or nonces
// - Need to cache the signature and message for later use?
// - If verifying with assets, is the asset transferable and prone to flash ownership attacks (e.g. one use per asset, etc)?
// - Other criteria needed for signing in? (e.g. whitelist / blacklist of addresses signing in)

//TODO: If using secrets proofs, are the contents valid? Above, we verified them to be well-formed from a cryptographic perspective, but you need to check the contents to ensure the proof is valid according to your application's rules.
//For example:
// - Verify the contents of the secret messages are correct
// - Verify the creator is who you expect
// - Verify the metadata is correct
// - Verify the on-chain anchors / update history are correct
// - Verify the update history is correct
</code></pre>

**Verification - Manual**

Behind the scenes, the verification uses the following endpoint. You may also verify by specifying all details via this endpoint, rather than an auth code.

```typescript
await BitBadgesApi.verifySignInGeneric({ ... });
```

**Pre-Fetching All Codes**

If you need to pre-fetch all codes before verification time (e.g. you are verifying in an offline setting), you can fetch all codes created via BitBadges for your client ID via the SDK below. Note that this

This is a paginated request, so you will need to specify the bookmark received from the previous request. This does not fetch or include any auth codes with redirect URIs (only ones stored in users' accounts via the manual approach).

```typescript
const res = await BitBadgesApi.getAuthCodesForAuthApp({ clientId, bookmark: '' });
console.log(res.blockinAuthCodes)
const blockinChallenge = res.blockinAuthCodes[0];
```

Note that we do not provide verification responses by default. You will need to verify each individually.  If you have time-dependent checks, note that by default, verification is done for the current time.

```typescript
await blockinChallenge.verify(api, ...);
await blockinChallenge.verifyOffline(...);

// or 

await BitBadgesApi.verifySignInGeneric({ ... });
await BitBadgesApi.getAuthCode({ code: blockinChallenge._docId, options: { ... });
```

## **IMPORTANT: What is verified vs not?**

It is important to note that calling any Blockin verification function only checks from a cryptographic standpoint and does not implement any application specific logic. Blockin handles checking the user's signature and verifying ownership of specified badges (if any). Any other custom requirements need to be handled by you separately (e.g. stamping users hands, checking IDs, etc.). It is also critical that you prevent replay attacks, man-in-the-middle attacks, and flash ownership attacks (if verifying with assets).

As an authentication provider, you should NOT assume the returned details are correct. It is critical you verify the message is in the expected format when received from the user. There is no guarantee that the user (or BitBadges) did not manipulate the original message and sign a manipulated one. Blockin verifies the message as-is, so a manipulated message will get a manipulated verification response.

Does check :white\_check\_mark:

* Signature is valid and signed by the address specified in the provided message.
* Asset ownership criteria is met for the address (if requested)
* Any options specified in the verify challenge options
* Secrets (if applicable) are well-formed from a cryptographic standpoint (data integrity, signed correctly) by the issuer. In other words, **secret.createdBy** issued the credential, and it is valid according to the BitBadges expected format.

Does not check :x:

* Additional app-specific criteria needed for signing in
* Any stateful data (e.g. handling sessions or checking nonces or preventing replay attacks or phishing attacks or flash ownership attacks)
* Does not handle sessions or check any session information
* The content of the challenge message is not checked by default except for well-formedness. You should assume the content may be manipulated and check it matches your desired auth details every time. Consider using the **expectedChallengeParams** options to help you.
* Does not check if **secret.createdBy** is the expected issuer (we check that they validly issued the secret with correct signatures, but only you know who this is supposed to be).
* Does not check the content of the secret messages or anything else about the secrets

**Phished Signature Attacks**

For most apps, it is important to verify that the unique challenge actually was generated by you and not by someone else.

This is typically done with a scheme that generates unique nonces for each challenge message, caches them, and checks that the random nonce was issued by you upon verification.

For example, a malicious party can phish a user into signing a challenge message for your app (https://example.com):

```
https://example.com wants you to sign in....

Nonce: somethinginvalid
```

Without additional checks, this is a valid challenge message for your app and would pass the BitBadges API verification from a cryptographic perspective. However, by checking the nonce, you can know that this sign in was not issued by you, and thus, the phishing attempt will not be successful (even if they do phish the signature).

You may be okay with taking this risk for ease of implementation, but it is strongly recommended to take this additional protective measure to ensure authentication is correct.

**Replay Attacks**

You shouldo also implement a replay attack prevention mechanism as well. Signatures will always be cryptographically valid, so it is important to prevent replay attacks in case the signatures get in the wrong hands.

This can be application dependent. See [here](https://blockin.gitbook.io/blockin/developer-docs/core-concepts) for more information.

Approaches

Time-Dependent Requests: Allow requests to be used in a short redeem window only, thus preventing replay attacks after a certain amount of time. This is neat because nothing needs to be cached like a nonce since it is all time fields.

```
const options = { issuedAtTimeWindowMs: 1000 * 60 * 2 } // 2 minutes
```

Unique Nonce Generation: Issue a unique **nonce** for each user and only allow it to be used once. Each time, you check used nonces against the requested one.

One Use per Address / Asset: Restrict sign ins to only allow one use per address or one use per unique badge ID.

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

## **All Verification Options**

Wherever you are calling a verification function, here are the options that you can pass in.

```typescript
export type VerifyChallengeOptions = {
    /**
     * Optionally define the expected details to check. If the challenge was edited and the details
     * do not match, the challenge will fail verification.
     */
    expectedChallengeParams?: Partial<ChallengeParams<NumberType>>;

    /**
     * Optional function to call before verification. This is useful to verify the challenge is
     * valid before proceeding with verification.
     *
     * Note you can use expectedChallengeParams to verify values equal as expected.
     *
     * This function is useful if you need to implement custom logic other than strict equality).
     * For example, assert that only one of assets A, B, or C are defined and not all three.
     */
    beforeVerification?: (params: ChallengeParams<NumberType>) => Promise<void>;

    /**
     * For verification of assets, instead of dynamically fetching the assets, you can specify a snapshot of the assets.
     *
     * This is useful if you have a snapshot, balances will not change, or you are verifying in an offline manner.
     */
    balancesSnapshot?: object;

    /**
     * If true, we do not check timestamps (expirationDate / notBefore). This is useful if you are verifying a challenge that is expected to be verified at a future time.
     */
    skipTimestampVerification?: boolean;

    /**
     * If true, we do not check asset ownership. This is useful if you are verifying a challenge that is expected to be verified at a future time.
     */
    skipAssetVerification?: boolean;

    /**
     * The earliest issued At ISO date string that is valid. For example, if you want to verify a challenge that was issued within the last minute, you can specify this to be 1 minute ago.
     */
    earliestIssuedAt?: string;

    /**
     * If set, we will verify the issuedAt is within this amount of ms ago (i.e. issuedAt >= Date.now() - issuedAtTimeWindowMs)
     */
    issuedAtTimeWindowMs?: number;

    /**
     * If true, we do not check the signature. You can pass in an undefined ChainDriver
     */
    skipSignatureVerification?: boolean;
    
    /**
     * If true, we will use this timestamp instead of the current time. UNIX milliseconds.
     */
    verificationTimeOverride?: number;
};
```
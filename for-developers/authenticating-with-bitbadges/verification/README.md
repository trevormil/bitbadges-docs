# Verification

From the prior pages, you should now have the necessary details for verification. You can verify the request using the following snippets.

```
{ message, signature, secretsProofs, publicKey } 
```

<pre class="language-tsx"><code class="lang-tsx">import { BlockinChallenge, BigIntify, BitBadgesApi, SecretsProof } from "bitbadgesjs-sdk";

const blockinChallenge = new BlockinChallenge(message).convert(BigIntify);
// Or const blockinChallenge = new BlockinChallenge(params).convert(BigIntify);
blockinRes.signature = doc.signature;
blockinRes.publicKey = doc.publicKey;
blockinRes.secretsProofs = doc.secretsProofs.map((proof) => new SecretsProof(proof));

const { params, address, chain, publicKey, message, signature, secretsProofs } = blockinChallenge;

const api = new BitBadgesApi(...);
const options = { ... }

const isVerified = await blockinChallenge.verify(api, options) // Verify with all options
const isVerified = await blockinChallenge.verifyOffline(api, options) // Verify without assets. Does not require an API call.

// verify and verifyOffline will set the verificationResponse field
// { success, errorMessage (if failure) }
console.log(blockinChallenge.verificationResponse); 
<strong>
</strong><strong>
</strong><strong>const isVerified = await blockinChallenge.verifyAssets(api) // Verify only the asset checks. Requires an API call.
</strong>const isVerified = await blockinChallenge.verifySignature() // Verify only the signature. Does not require an API call.
const isVerified = await blockinChallenge.verifySecretsProofs() // Verify only the secrets proofs. Does not require an API call.

if (!isVerified) throw new Error("Oops! Could not verify.");

// Behind the scenes of the above abstracted class, we use the following snippets
// if you are looking to self-implement certain parts:
// - BitBadgesApi.verifySignInGeneric()
// - BitBadgesApi.verifyAssetsGeneric() // Or, you can query badge balances / address lists directly and check yourself
// - import { verifySignature, verifySecretsProofs } from "bitbadgesjs-sdk";
</code></pre>

**IMPORTANT: What is verified vs not?**

It is important to note that calling any Blockin verification function only checks from a cryptographic standpoint and does not implement any application specific logic. Blockin handles checking the user's signature and verifying ownership of specified badges (if any). Any other custom requirements need to be handled by you separately (e.g. stamping users hands, checking IDs, etc.). It is also critical that you prevent replay attacks, man-in-the-middle attacks, and flash ownership attacks (if verifying with assets).&#x20;

As an authentication provider, you should NOT assume the returned details are correct for your application. It is critical you verify the message is in the expected format when received from the user. There is no guarantee that the user (or BitBadges) did not manipulate the original message and sign a manipulated one. Blockin verifies the message as-is, so a manipulated message will get a manipulated verification response.

Does check :white\_check\_mark:

* Signature is valid and signed by the address specified in the provided message.
* Asset ownership criteria is met for the address (if requested)
* Any options specified below
* Secrets (if applicable) are well-formed from a cryptographic standpoint (data integrity, signed correctly)

Does not check :x:

* Additional criteria needed for signing in
* Any stateful data (e.g. handling sessions or preventing replay attacks or flash ownership attacks)
* Does not handle sessions or check any session information
* The content of the message. You should assume the content may be manipulated and check it every time. Consider using the **expectedChallengeParams** options to help you.
* The contents / creator of the secrets (if applicable). We check that they are signed correctly, but it is up to you to verify the contents / creator are correct. See [here](secrets-contents.md) for more information.

**Replay Attacks**

You need to also implement a replay attack prevention mechanism as well. This can be application dependent.

Recommended: Most use cases will be for digital authentication that is instant (sign time -> auth time) with secure communication channels, such as gating a website. If this is applicable to you, we recommend implementing time-dependent windows (e.g. you have 1 minute to "redeem" you sign in). This can be implemented with the **earliestIssuedAt** option of verifyChallenge. This will check the **issuedAt** field of the challenge and assert it is recent enough. If it is not, authentication will fail (thus preventing replay attacks after a certain time).

**Flash Ownership Attacks**

If authenticating with assets, you should be aware of flash ownership attacks. Basically, two sign ins at different times would be approved if the badge is transferred between the time of the first sign in and the second one.&#x20;

**Frontend vs Backend?**

Typically, your backend is the one that authenticates users and handles sessions, so verification is often done there. However, verifying on frontend vs backend is up to you and your application's requirements.&#x20;

You can also consider a hybrid approach (e.g. check certain stuff and fail early on frontend). For example, you may do:

```tsx
//Frontend - Fails early if offline only checks fail
const isVerified = await blockinChallenge.verifyOffline(api, options) // Verify without assets. Does not require an API call.

//Backend - Full verification checking everything
const isVerified = await blockinChallenge.verify(api, options) // Verify with all options
```

**All Verification Options**

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
  balancesSnapshot?: object

  /**
   * If true, we do not check timestamps (expirationDate / notBefore). This is useful if you are verifying a challenge that is expected to be verified at a future time.
   */
  skipTimestampVerification?: boolean,

  /**
   * If true, we do not check asset ownership. This is useful if you are verifying a challenge that is expected to be verified at a future time.
   */
  skipAssetVerification?: boolean,

  /**
   * The earliest issued At ISO date string that is valid. For example, if you want to verify a challenge that was issued within the last minute, you can specify this to be 1 minute ago.
   */
  earliestIssuedAt?: string,

  /**
   * If set, we will verify the issuedAt is within this amount of ms ago (i.e. issuedAt >= Date.now() - issuedAtTimeWindowMs)
   */
  issuedAtTimeWindowMs?: number,

  /**
   * If true, we do not check the signature. You can pass in an undefined ChainDriver
   */
  skipSignatureVerification?: boolean,
}
```


# Sign In with BitBadges Callback

The callback approach is useful if you want to obtain the authentication details as soon as the user generates them.  For immediate authentication, the callback approach is the only option. For delayed authentication, this is useful if you want to cache the details upon the user signing them, rather than fetching from the API at verification time (e.g. the [No Callback method](no-callback.md)).

To enable callbacks, the popupParams must have **callbackRequired** set to true. We will throw if **storeInAccount** is set to true as well because BitBadges should not need to store anything if a) it is immediate authentication or b) you are caching the details.

With the callback, the user should never even see the code (or resulting signature). Everything should be handled behind the scenes.&#x20;

**How do callbacks work?**

A high-level overview is:

1. From your custom frontend, you redirect to the authentication code URL via a button, window.open, \<a> href, etc. In our case, we use the Sign In with BitBadges button. This will open up a child window which is "linked" to the parent window.
2. The user will be waked through the authentication process. Upon completion, the core details needed for verification are passed back to the parent window.

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**Implementation**

If you are using the Sign In with BitBadges button, the URL generation is handled behind the scenes for you, but you will have to pass your **popupParams** into the component. Upon successful completion, you can receive the details via the **onSignAndBlockinVerify** callback. See [https://github.com/Blockin-Labs/blockin/tree/main/src/ui](https://github.com/Blockin-Labs/blockin/tree/main/src/ui) for the code.

```tsx
import { SignInWithBitBadges as SignInWithBitBadgesButton } from 'blockin/dist/ui';
import { BigIntify, BlockinChallenge, NumberType, Numberify, getChainForAddress, iSecretsProof } from 'bitbadgesjs-sdk';

...

<SignInWithBitBadgesButton
  //TODO: Customize your popup parameters here. See the documentation for more details.
  popupParams={popupParams}
  //This will only be called if callbackRequired is true in the popupParams
  onSignAndBlockinVerify={async (message, signature, verificationResponse, secretsProofs, publicKey) => {
    const blockinChallenge = new BlockinChallenge(message).convert(BigIntify);
    blockinChallenge.setSecretsProofs(secretsProofs);
    blockinChallenge.setSignature(signature, publicKey);
    blockinChallenge.setVerificationResponse(verificationResponse);

    if (!verificationResponse.success) throw new Error(verificationResponse.errorMessage ?? 'Error');

    // If you want to, you can self verify here for extra security. You can verify everything or just individual parts of the challenge.
    // It is typically best to verify the (message, signature) yourself on the backend, but it can also be done here. 
    // This uses the BitBadges SDK, but you may also self-implement your own verification logic as well.
    // - const isVerified = await blockinChallenge.verify() - Verify with all options
    // - const isVerified = await blockinChallenge.verifyOffline() - Verify without assets. Does not require an API call.
    // - const isVerified = await blockinChallenge.verifyAssets() - Verify only the asset checks. Requires an API call.
    // - const isVerified = await blockinChallenge.verifySignature() - Verify only the signature. Does not require an API call.
    // - const isVerified = await blockinChallenge.verifySecretsProofs() - Verify only the secrets proofs. Does not require an API call.

    //The auth code ID is what can be used to fetch the user generated auth code from the BitBadges API (if storeInAccount = true).
    //However, note that this is just extra because it will just return the same data you already have in blockinChallenge.
    //The auth code ID is more useful without a callback implementation because you do not have those details yet.
    // - const res = await BitBadgesApi.getAuthCode({ id: _authCodeId, options: { ...verifyOptions } });

    //TODO: Handle other checks and logic here
    // Again, this can be done here or on your frontend depending on your use case.
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

    

    // TODO: Verify on your backend and handle sessions or cache the necessary details for use at verification time (if verification time is not immediate)
    await verifyOnBackend(message, signature, { siwbb: true }, verifyOptions, blockinChallenge.secretsProofs?.map(x => x.convert(BigIntify)) ?? []);

    //TODO: Handle any other frontend logic here
  }}
/>
```

**Trusting the Verification Response?**

**Note that trusting this value may or may not be applicable, depending on your application's architecture, because it is executed on the client-side or frontend.**

For example, in a typical full-stack application, this check is done via the popup (frontend or client-side), and you will eventually authenticate users on your backend (server-side). From your backend, you need to ensure that the results from the client-side check are correct and not manipulated when passed to the backend. This is possible (CORS or other methods), but it can get tricky and is not recommended.

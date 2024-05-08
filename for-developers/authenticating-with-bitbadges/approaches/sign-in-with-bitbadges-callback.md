# Sign In with BitBadges Callback

The callback approach is useful if you want to obtain the authentication details as soon as the user generates them.  For immediate authentication, the callback approach is the only option. For delayed authentication, this is useful if you want to cache the details upon the user signing them, rather than fetching from the API at verification time (e.g. the [No Callback method](manual.md)).

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

// ... 

<SignInWithBitBadgesButton
  popupParams={popupParams}
  //Note this will only be called if callbackRequired is true in the popupParams
  onSignAndBlockinVerify={async (message, signature, secretsProofs, publicKey) => {
    const blockinChallenge = new BlockinChallenge(message).convert(BigIntify);
    blockinChallenge.setSecretsProofs(secretsProofs);
    blockinChallenge.setSignature(signature, publicKey);

    // TODO: Cache the values if you are implementing delayed authentication w/ cached values
    
    // TODO: Handle your verification logic 
  }}
/>
```


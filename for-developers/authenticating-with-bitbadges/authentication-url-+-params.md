# Authentication URL + Params

All implementations will have a unique authentication URL custom to your application's parameters. Users will need to navigate to this URL in order to sign the challenge message. The different parameters will be specific to your implementation and requirements.

The base URL is [https://bitbadges.io/auth/codegen](https://bitbadges.io/auth/codegen) and parameters will be appended to it.

```
https://bitbadges.io/auth/codegen?name=Event&description=...
```

The URL expects the parameters to follow the following interface.

```typescript
const popupParams: {
    name: string;
    description: string;
    image: string;
    challengeParams: ChallengeParams<NumberType>;
    allowAddressSelect: boolean;
    verifyOptions?: VerifyChallengeOptions;
    skipVerify?: boolean;
    expectVerifySuccess?: boolean;
    discord?: {
        clientId: string;
        redirectUri: string;
    };
    expectSecretsProofs?: boolean;
    onlyProofs?: boolean;
} =  { ... }
```

See [ChallengeParams](https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html) (for inherited fields, should also follow the [EIP-4361 standard](https://docs.login.xyz/general-information/siwe-overview/eip-4361)) and [VerifyChallengeOptions](https://blockin-labs.github.io/blockin/docs/types/VerifyChallengeOptions.html) for the full type definitions of the Blcokin interfaces.&#x20;

#### Cached at Click Time <a href="#cached-at-click-time" id="cached-at-click-time"></a>

All parameters are cached at the time the URL is opened. Thus, you cannot perform dynamic checks using these fields, such as **earliestIssuedAt** is within 5 minutes ago using the **current** time. Consider either implementing dynamic checks on your end or use dynamic fields like **verifyOptions.issuedAtTimeWindowMs.**



#### &#x20; <a href="#generation-options" id="generation-options"></a>





Asset Ownership Requirements



**Verify Options**



**challengeParams** are the Blockin parameters for the sign-in challenge. See here for the full type definition [https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html](https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html). These will make up the base message that is expected. Here, you include all details about the sign-in like expiration date, badges to be owned, etc.

If **allowAddressSelect** is true, we will handle the address selection on our end. Meaning, we override challengeParams.address and challengeParams.chain with the user's selected address / chain, respectively. If false, we enforce that the connected address is exactly what is specified in **challengeParams**.

**name**, **description**, and **image** follow the base metadata format. These will be used for UI purposes and displaying everything nicely to the user. They are not included anywhere in the message.

**callbackRequired** set to true means we will return the (message, signature) pair to the window that directed to the site (see below). The window will auto-close upon signing. In this case, the user should not even see the code. Because of this, we recommend setting **storeInAccount** to false. You should handle the (message, signature) pair as necessary.

**storeInAccount** set to true means we will store the code in the user's BitBadges account via the Authentication Codes page. For manual (see below), this should be true. For callbacks (see below), this is typically false to avoid extra confusion for the user. If not a callback and this is false, the code will be one view-only and must be saved elsewhere.

**skipVerify** set to true means we will skip the Blockin verification call for the callback.

**verifyOptions** The Blockin verifyChallenge options when calling verify for the callback. See here for the full type definition [https://blockin-labs.github.io/blockin/docs/types/VerifyChallengeOptions.html](https://blockin-labs.github.io/blockin/docs/types/VerifyChallengeOptions.html).

**expectVerifySuccess** lets us know whether or not the verification (according to verifyOptions) is expected to return success. If so, we are able to check and catch certain errors and warn the user before they sign rather than after. Some use cases, however, may not be expected to pass at sign time, such as pre-generating a QR code for a later time. This option defaults to false.

**expectSecretsProofs** will tell us whether you expect the user to provide additional proof of credentials (i.e. saved secrets in their BitBadges account) to be verified. **onlyProofs** means you do not even expect a signature to be presented (only using the UI for obtaining proofs). See [here](https://docs.bitbadges.io/for-developers/core-concepts/verifiable-secrets) for verifying proofs.

#### &#x20; <a href="#cached-at-click-time" id="cached-at-click-time"></a>

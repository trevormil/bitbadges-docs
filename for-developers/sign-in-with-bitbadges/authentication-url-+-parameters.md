# Authentication URL + Parameters

\
Each implementation will feature a unique authentication URL tailored to your application's needs. Users must visit this URL to sign the challenge message. Parameters will vary based on your specific implementation and requirements.

The base URL is [https://bitbadges.io/auth/codegen](https://bitbadges.io/auth/codegen), with parameters appended to it. For instance:

```vbnet
https://bitbadges.io/auth/codegen?name=Event&description=...
```

This URL structure adheres to the following interface:

* **Base URL**: [https://bitbadges.io/auth/codegen](https://bitbadges.io/auth/codegen)
* **Parameters**: Custom parameters specific to your implementation.

If you need assistance generating these parameters, you can use the helper tool available at [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen).

```typescript
const popupParams: {
    name: string;
    description: string;
    image: string;
    challengeParams: ChallengeParams<NumberType>;
    allowAddressSelect: boolean;
    autoGenerateNonce: boolean;
    
    verifyOptions?: VerifyChallengeOptions;
    expectVerifySuccess?: boolean;
    
    discord?: {
        clientId: string;
        redirectUri: string;
    };
    
    expectSecretsProofs?: boolean;
    onlyProofs?: boolean;
} =  { ... }
```

#### Cached at Click Time <a href="#cached-at-click-time" id="cached-at-click-time"></a>

All parameters are cached at the time the URL is opened. Consider either implementing dynamic checks on your end or make sure to use dynamic fields like **verifyOptions.issuedAtTimeWindowMs.**

### **Parameter Options**

**Challenge Parameters**

The core details about your challenge message and authentication request are the challenge parameters. We have dedicated that to its own page to fully explain.

{% content-ref url="challenge-parameters.md" %}
[challenge-parameters.md](challenge-parameters.md)
{% endcontent-ref %}

```typescript
const popupParams = {
    ...,
    challengeParams: { 
        domain: 'https://bitbadges.io',
        statement: 'This request ...',
        address, //0x or cosmos1 or bc1 or other supported address
        uri: 'https://bitbadges.io',
        nonce: '*',
        expirationDate: new Date(Date.now() + 1000 * 60 * 60 * 24 * 14).toISOString(),
        notBefore: undefined,
        resources: ['Full Access: Full access to all features.'],
        assetOwnershipRequirements: undefined
    },
    allowAddressSelect: true,
    autoGenerateNonce: false
}
```

**Metadata**

```typescript
const popupParams = {
    ...,
    name: 'Event',
    description: 'Grants access to the event',
    image: 'ipfs://...'
}
```

**name**, **description**, and **image** follow the base metadata format. These will only be used for UI purposes and displaying everything nicely to the user. They are not included anywhere in the message.

**Simulations**

```typescript
const popupParams = {
    ...,
    verifyOptions: { 
        expectedChallengeParams: { ... ],
        skipSignatureVerification: false,
        ...
    },
    expectVerifySuccess: true
}
```

For a better user experience and interface, we can simulate certain aspects about the request (asset ownership, etc) if you pass in **expectVerifySuccess**. This lets us check and catch certain errors and warn the user before they sign rather than after. Some use cases, however, may not be expected to pass at sign time, such as pre-generating a QR code for a later time.&#x20;

The default is false. If true, we check only a few details out of the box, but passing in **verifyOptions** will let us know the expected verification options and can help us further enhance our simulation feature. See Verification page for all options explained.&#x20;

**Secrets**

```typescript
const popupParams = {
    ...,
    expectSecretsProofs: true,
    onlyProofs: false
}
```

**expectSecretsProofs** will tell us whether you expect the user to provide additional proof of credentials (i.e. saved secrets in their BitBadges account) to be verified. Note this will require the user to be signed in to BitBadges to fetch the proofs (which may be an additional step in the process).

**onlyProofs** means you do not even expect a signature to be presented (only using the UI for obtaining proofs).&#x20;

**Discord**

The Discord aprameters are used if implementing a gated Discord channel. See [the tutorial](../authenticating-with-bitbadges/verification/badge-gating-discord-servers.md) for more information.

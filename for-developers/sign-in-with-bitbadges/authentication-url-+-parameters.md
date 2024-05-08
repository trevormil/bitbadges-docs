# Authentication URL + Parameters

All implementations will have a unique authentication URL custom to your application's parameters. Users will need to navigate to this URL in order to sign the challenge message. The different parameters will be specific to your implementation and requirements.

The base URL is [https://bitbadges.io/auth/codegen](https://bitbadges.io/auth/codegen) and parameters will be appended to it. Click here for a [demo](https://bitbadges.io/auth/codegen?name=BitBadges%20Sign%20In%20Example\&description=This%20is%20just%20an%20example%20for%20signing%20in%20with%20BitBadges.%20Websites%20can%20use%20this%20to%20authenticate%20users%20by%20outsourcing%20all%20authentication%20logic%20to%20a%20Sign%20In%20with%20BitBadges%20popup.\&image=https://bitbadges.io/images/bitbadgeslogo.png\&allowAddressSelect=true\&skipVerify=true\&expectSecretsProofs=true\&challengeParams=%7B%22domain%22%3A%22https%3A%2F%2Fxyz.com%22%2C%22statement%22%3A%22This%20is%20just%20an%20example%20of%20Sign%20In%20with%20BitBadges.%20Nothing%20is%20done%20with%20this%20sign%20in%20attempt.%22%2C%22address%22%3A%22%22%2C%22uri%22%3A%22https%3A%2F%2Fxyz.com%22%2C%22nonce%22%3A%22abc123%22%2C%22issuedAt%22%3A%222024-05-07T16%3A40%3A35.694Z%22%2C%22expirationDate%22%3A%222024-05-14T16%3A40%3A35.694Z%22%2C%22resources%22%3A%5B%5D%2C%22assetOwnershipRequirements%22%3A%7B%22assets%22%3A%5B%7B%22chain%22%3A%22BitBadges%22%2C%22collectionId%22%3A1%2C%22assetIds%22%3A%5B%7B%22start%22%3A1%2C%22end%22%3A1%7D%5D%2C%22mustOwnAmounts%22%3A%7B%22start%22%3A1%2C%22end%22%3A1%7D%2C%22ownershipTimes%22%3A%5B%5D%7D%5D%7D%7D\&callbackRequired=true&).

```
https://bitbadges.io/auth/codegen?name=Event&description=...
```

The URL expects the parameters to follow the following interface. If you want a helper tool to generate the parameters, visit https://bitbadges.io/auth/linkgen.

```typescript
const popupParams: {
    name: string;
    description: string;
    image: string;
    challengeParams: ChallengeParams<NumberType>;
    allowAddressSelect: boolean;
    
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

See the subpages for more information on specific fields.

#### Cached at Click Time <a href="#cached-at-click-time" id="cached-at-click-time"></a>

All parameters are cached at the time the URL is opened. Thus, you cannot perform dynamic checks using static fields, such as **earliestIssuedAt** is within 5 minutes ago. Consider either implementing dynamic checks on your end or use dynamic fields like **verifyOptions.issuedAtTimeWindowMs.**

### **Fields**

**Challenge Parameters**

The core details about your challenge message and authentication request are the challenge parameters. We have dedicated that to its own page to fully explain.

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
    allowAddressSelect: true
}
```

{% content-ref url="challenge-parameters.md" %}
[challenge-parameters.md](challenge-parameters.md)
{% endcontent-ref %}

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

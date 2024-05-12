# Authentication URL + Parameters

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
import { CodeGenQueryParams } from 'bitbadgesjs-sdk';

const popupParams: {
    name: string;
    description: string;
    image: string;

    clientId: string;
    redirectUri?: string;
    state?: string;

    challengeParams: ChallengeParams<NumberType>;
    allowAddressSelect?: boolean;
    autoGenerateNonce?: boolean;

    verifyOptions?: VerifyChallengeOptions;
    expectVerifySuccess?: boolean;

    otherSignIns?: ('discord' | 'twitter' | 'github' | 'google')[];

    expectSecretsPresentations?: boolean;
    onlyProofs?: boolean;
} =  { ... } as CodeGenQueryParams;
```

#### Cached at Click Time <a href="#cached-at-click-time" id="cached-at-click-time"></a>

All parameters are cached at the time the URL is opened. Consider either implementing dynamic checks on your end or make sure to use dynamic fields like **verifyOptions.issuedAtTimeWindowMs.**

### **Parameter Options**

**App Configuration**

All BitBadges authentication requests must specify an app that the request is for. Apps can be created and managed at [https://bitbadges.io/developer](https://bitbadges.io/developer).

Each app is identified by the **clientId,** which is mandatory. The **redirectUri** is a critical component in the BitBadges authentication process, acting as the destination URL to which authentication details are transmitted for verification by your application. This URI must be precisely defined in your app's settings on BitBadges, ensuring a secure and expected pathway for the authentication flow. Lastly, **state** is additional information that may be passed to the redirectUri (if applicable).

For instant authentication, the **redirectUri** is mandatory, and we do not store the code in the user's account. The code should all be handled behind the scenes for the user.

If you are implementing delayed authentication where the user is to present you their authentication code manually, you can leave the **redirectUri** blank.

If it is blank, we will store the code in the user's account under the Authentication Codes tab. They are to manually present you the code.

```typescript
const popupParams = {
    ...,

    clientId: '...',
    redirectUri: 'https://...',
    state: ''
}
```

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
        address, // 0x or cosmos1 or bc1 or other supported address
        uri: 'https://bitbadges.io',
        nonce: '*',
        
        //Optional
        expirationDate: new Date(Date.now() + 1000 * 60 * 60 * 24 * 14).toISOString(),
        notBefore: undefined,
        resources: ['Full Access: Full access to all features.'],
        assetOwnershipRequirements: undefined
    },
    allowAddressSelect: true,
    autoGenerateNonce: false
}
```

**Other Sign Ins**

```typescript
const popupParams = {
    ...,
    otherSignIns: ['discord']
}
```

If **otherSignIns** is defined, we will additionally make the user sign in to the requested services and pass you their connected username / account ID with the authentication details. Note we do not pass any access tokens or private details (simply username / account ID).

This can be used to implement, for example, badge gating Discord servers. Check badges, address ownership, and Discord account ownership via here, then grant roles based on successful authentication.

```typescript
{
    discord?: { username: string; discriminator?: string | undefined; id: string } | undefined;
    github?: { username: string; id: string } | undefined;
    google?: { username: string; id: string } | undefined;
    twitter?: { username: string; id: string } | undefined;
}
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

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

For a better user experience and interface, we can simulate certain aspects about the request (asset ownership, etc) if you pass in **expectVerifySuccess**. This lets us check and catch certain errors and warn the user before they sign rather than after. Some use cases, however, may not be expected to pass at sign time, such as pre-generating a QR code for a later time.

The default is false. If true, we check only a few details out of the box, but passing in **verifyOptions** will let us know the expected verification options and can help us further enhance our simulation feature. See Verification page for all options explained.

**Secrets**

```typescript
const popupParams = {
    ...,
    expectSecretsPresentations: true
}
```

**expectSecretsPresentations** will tell us whether you expect the user to provide additional proof of credentials (i.e. saved secrets in their BitBadges account) to be verified. Note this will require the user to be signed in to BitBadges to fetch the proofs (which may be an additional step in the process).

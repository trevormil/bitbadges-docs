# Authentication URL + Parameters

Each implementation will feature a unique authentication URL tailored to your application's needs. Users must visit this URL to sign the challenge message. Parameters will vary based on your specific implementation and requirements.

The base URL is [https://bitbadges.io/siwbb/authorize](https://bitbadges.io/siwbb/authorize), with parameters appended to it. For instance:

```vbnet
https://bitbadges.io/siwbb/authorize?name=Event&description=...
```

This URL structure adheres to the following interface:

* **Base URL**: [https://bitbadges.io/siwbb/authorize](https://bitbadges.io/siwbb/authorize)
* **Parameters**: Custom parameters specific to your implementation.

```typescript
import { CodeGenQueryParams } from 'bitbadgesjs-sdk';

export interface CodeGenQueryParams {
  ownershipRequirements?: AssetConditionGroup<NumberType>;
  expectVerifySuccess?: boolean;

  name?: string;
  description?: string;
  image?: string;

  otherSignIns?: ('discord' | 'twitter' | 'github' | 'google')[];

  redirect_uri?: string;
  client_id: string;
  state?: string;
  scope?: string;

  expectAttestationsPresentations?: boolean;
}
```

We recommend using the helper tool available at [https://bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen).

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### **Parameter Options**

**App Configuration**

All BitBadges authentication requests must specify an app that the request is for. Apps can be created and managed at [https://bitbadges.io/developer](https://bitbadges.io/developer). You can create multiple apps for different use cases.

Each app is identified by the client ID**,** which is mandatory. The redirect URI is a critical component in the BitBadges authentication process, acting as the destination URL to which authentication details are transmitted for verification by your application. This URI must be precisely defined in your app's settings on BitBadges, ensuring a secure and expected pathway for the authentication flow. Lastly, **state** is additional information that may be passed to the redirect URI (if applicable).

For instant authentication, the redirect URI is mandatory, and we do not store the code in the user's account. The code should all be handled behind the scenes for the user.

If you are implementing delayed authentication where the user is to present you their code manually, you can leave the redirect URI blank.

If it is blank, we will store the code in the user's account under the Authentication Codes tab. They are to manually present you the code.

```typescript
const popupParams = {
    ...,

    client_id: '...',
    redirect_uri: 'https://...',
    state: ''
}
```

**Scopes**

```typescript
const popupParams = {
    ...,

    scope: 'Complete Claims,Read Address Lists'
}
```

Visit https://bitbadges.io/auth/linkgen to see the full list of approved scopes. You only should specify the labels (scope names). Let us know if you would like us to add other scopes. These should be comma delimited. By default, we always return the user crypto address even if no scope is specified. Scopes must be used with instant authentication an dredirect URIs. Delayed QR codes are not supported if scopes are present.

By specifying scopes, we will give you an access token / refresh token to use. If no scopes are specified, we will not give you such tokens.

**Ownership Requirements**

Which badges / assets should we verify that the user owns? We have dedicated that to its own page to fully explain.

{% content-ref url="challenge-parameters.md" %}
[challenge-parameters.md](challenge-parameters.md)
{% endcontent-ref %}

```typescript
const popupParams = {
    ...,

    ownershipRequirements: { ... }
}
```

Its important that you verify the ownership requirements are correct on your end during verification because this is a client-side parameter that may be changed. More is explained on the verification page.

Since badges can be queried publicly, you may consider also leaving this step out of the sign in flow and verifying any necessary requirements after the fact too.&#x20;

**Other Sign Ins**

```typescript
const popupParams = {
    ...,
    otherSignIns: ['discord']
}
```

If **otherSignIns** is defined, we will additionally make the user sign in to the requested services and pass you their connected username / account ID with the authentication details. Note we do not pass any access tokens or private details (simply username / account ID).

This can be used to implement, for example, badge gating Discord servers. Check badges, address ownership, and Discord account ownership via here, then grant roles based on successful authentication.

Its important that you verify this correct on your end during verification because this is a client-side parameter that may be changed. More is explained on the verification page.

```typescript
{
    discord?: { username: string; discriminator?: string | undefined; id: string } | undefined;
    github?: { username: string; id: string } | undefined;
    google?: { username: string; id: string } | undefined;
    twitter?: { username: string; id: string } | undefined;
}
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Metadata**

```typescript
const popupParams = {
    ...,
    name: 'Event',
    description: 'Grants access to the event',
    image: 'ipfs://...'
}
```

**name**, **description**, and **image** follow the base metadata format. These will only be used for UI purposes and displaying everything nicely to the user.

**Simulations**

```typescript
const popupParams = {
    ...,
    verifyOptions: {
        ...
    },
    expectVerifySuccess: true
}
```

For a better user experience and interface, we can simulate certain aspects about the request (asset ownership, etc) if you pass in **expectVerifySuccess**. This lets us check and catch certain errors and warn the user before they sign rather than after. Some use cases, however, may not be expected to pass at sign time, such as pre-generating a QR code for a later time.

The default is false. If true, we check only a few details out of the box, but passing in **verifyOptions** will let us know the expected verification options and can help us further enhance our simulation feature. See Verification page for all options explained.

**Attestations**

```typescript
const popupParams = {
    ...,
    expectAttestationsPresentations: true
}
```

**expectAttestationsPresentations** will tell us whether you expect the user to provide additional proof of credentials (i.e. saved attestations in their BitBadges account) to be verified.

# Configuration

Each implementation will feature a unique authentication URL tailored to your application's needs. Users will visit this URL to authenticate and receive an authorization code which you will use. This code  will be behind the scenes for digital flows or the actual QR code for in-person / delayed flows.

Parameters will vary based on your specific implementation and requirements. The base URL is [https://bitbadges.io/siwbb/authorize](https://bitbadges.io/siwbb/authorize), with parameters appended to it. For instance:

```vbnet
https://bitbadges.io/siwbb/authorize?client_id=...
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
  
  claimId?: string;
  hideIfAlreadyClaimed?: boolean;
}
```

### **Parameter Options**

**App Configuration**

All BitBadges authentication requests must specify an app that the request is for. Apps can be created and managed at [https://bitbadges.io/developer](https://bitbadges.io/developer). You can create multiple apps for different use cases.

Each app is identified by the client I&#x44;**,** which is mandatory. The redirect URI is a critical component in the BitBadges authentication process, acting as the destination URL to which authentication details are transmitted for verification by your application. This URI must be precisely defined in your app's settings on BitBadges, ensuring a secure and expected pathway for the authentication flow. Lastly, **state** is additional information that may be passed to the redirect URI (if applicable).

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

**Claims**

Which claim must the user pass to be successfully granted access? This is a super powerful feature because your sign ins now have access to any integration, Zapier, and are completely customizable.

It is also powerful because certain state can be outsourced to the claim (e.g. max 1 use per address, etc). This may eliminate some of the need for maintianing this on your end.

SIWBB claims can be created and managed in the developer portal

**claimId** is the ID of the claim to display. We disable the sign in button until this claim is successful. **hideIfAlreadyClaimed** lets us know to not display the claim to the user if they have already succesfully passed a minimum of 1 times. Note that you can also just not pass the claimId in the params if you do not want to display it for specific users / cases.

IMPORTANT: This parameter is just for user display purposes and is not cached with the request. You will have to check claim requirements / ID server-side during the verification step.

For all features / tutorials, see below.

{% content-ref url="../../../overview/claim-builder/" %}
[claim-builder](../../../overview/claim-builder/)
{% endcontent-ref %}

```typescript
const popupParams = {
    ...,

    claimId: "0ab12...",
    hideIfAlreadyClaimed: true
}
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Ownership Requirements**

Which badges / assets should we verify that the user owns? We have dedicated that to its own page to fully explain.

IMPORTANT: This parameter is just for user display purposes and is not cached with the request. You will have to respecify the requirements server-side during the verification step.

{% content-ref url="challenge-parameters.md" %}
[challenge-parameters.md](challenge-parameters.md)
{% endcontent-ref %}

```typescript
const popupParams = {
    ...,

    ownershipRequirements: { ... },
    expectVerifySuccess: true
}
```

Since badges can be queried publicly, you may consider also leaving this step out of the sign in flow and verifying any necessary requirements behind the scenes. Or, self-implement your own solution. Or, instead provide a custom description specifying requirements rather than this. Or, check this within your attached BitBadges claim rather than here. We aim to be as flexible as possible.

By default, we simulate and warn the user if the ownership requirements fail. This can be controlled with **expectVerifySuccess**. Some use cases may not be expected to pass ownership requirements at sign time, such as if assets are not distributed yet.

**Attestations**

```typescript
const popupParams = {
    ...,
    expectAttestationsPresentations: true
}
```

**expectAttestationsPresentations** will tell us whether you expect the user to provide additional proof of credentials (i.e. saved attestations in their BitBadges account) to be verified.

**Other Sign Ins**

```typescript
const popupParams = {
    ...,
    otherSignIns: ['discord']
}
```

If **otherSignIns** is defined, we will additionally make the user sign in to the requested services and pass you their connected username / account ID with the authentication details. Note we DO NOT pass any access tokens or private details (simply username / account ID).

This can be used to implement, for example, badge gating Discord servers. Check badges, address ownership, and Discord account ownership via here, then grant roles based on successful authentication.

Its important that you verify you receive responses (e.g. response.otherSignIns.discord.username !== undefined) for these and do not trust the verification response blindly. This is because this is a client-side parameter that may be changed. More is explained on the verification page.

```typescript
{
    discord?: { username: string; discriminator?: string | undefined; id: string } | undefined;
    github?: { username: string; id: string } | undefined;
    google?: { username: string; id: string } | undefined;
    twitter?: { username: string; id: string } | undefined;
}
```

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Custom Metadata**

```typescript
const popupParams = {
    ...,
    name: 'Event',
    description: 'Grants access to the event',
    image: 'ipfs://...'
}
```

**name**, **description**, and **image** follow the base metadata format. These will only be used for UI purposes and displaying everything nicely to the user.

These are optional. By default, we use your app's name, image, and description.

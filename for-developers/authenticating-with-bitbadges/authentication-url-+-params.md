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

See the subpages for more information on specific fields.

#### Cached at Click Time <a href="#cached-at-click-time" id="cached-at-click-time"></a>

All parameters are cached at the time the URL is opened. Thus, you cannot perform dynamic checks using static fields, such as **earliestIssuedAt** is within 5 minutes ago. Consider either implementing dynamic checks on your end or use dynamic fields like **verifyOptions.issuedAtTimeWindowMs.**

# Verification

**Verification Options**

Wherever you are calling a verification function, here are the options that you can pass in.

```typescript
// Example
{
  expectedChallengeParams: {
    domain: 'https://bitbadges.io',
    uri: 'https://bitbadges.io',
  },
  skipTimestammpVerification: false
}
```

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

**Expected Message + Attack Prevention Checks**

It is critical you verify the message is in the expected format when received from the user. Blockin verifies the message as-is, so a manipulated message will get a manipulated verification response. Consider using the **expectedChallengeParams** option to ensure eveyrthing is as intended.

**Replay Attacks**

You need to also implement a replay attack prevention mechanism as well. This can be application dependent.

Recommended: Most use cases will be for digital authentication that is instant (sign time -> auth time) with secure communication channels, such as gating a website. If this is applicable to you, we recommend implementing time-dependent windows (e.g. you have 1 minute to "redeem" you sign in). This can be implemented with the **earliestIssuedAt** option of verifyChallenge. This will check the **issuedAt** field of the challenge and assert it is recent enough. If it is not, authentication will fail (thus preventing replay attacks after a certain time).

**Flash Ownership Attacks**

If authenticating with assets, you should be aware of flash ownership attacks. Basically, two sign ins at different times would be approved if the badge is transferred between the time of the first sign in and the second one.&#x20;

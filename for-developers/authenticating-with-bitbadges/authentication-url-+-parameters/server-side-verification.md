# Server-Side Verification

**skipVerify** set to true means we will skip the Blockin verification call for the callback.

**verifyOptions** The Blockin verifyChallenge options when calling verify for the callback. See here for the full type definition [https://blockin-labs.github.io/blockin/docs/types/VerifyChallengeOptions.html](https://blockin-labs.github.io/blockin/docs/types/VerifyChallengeOptions.html).

**expectVerifySuccess** lets us know whether or not the verification (according to verifyOptions) is expected to return success. If so, we are able to check and catch certain errors and warn the user before they sign rather than after. Some use cases, however, may not be expected to pass at sign time, such as pre-generating a QR code for a later time. This option defaults to false.

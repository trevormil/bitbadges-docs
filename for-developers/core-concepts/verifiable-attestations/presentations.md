# Presentations

Pre-Readings: [Verifiable Attestations](./)

The last part of attestations is the presentation of the proof to the verifier. The presentation should reveal or give all necessary information needed for the verifier to be able to verify the integrity of the attestation.

With presentations, there are a couple things that need to be considered.

* If a malicious party gets a cryptographic signature, the signature will still be the same and valid. Thus, it is important to protect against replay attacks, man in the middle attacks, and verify any address ownership as needed. Thus, verification is typically a two-step approach, 1) verify the credential and 2) verify the address of the recipient.
* While the attestation / proof itself can prove the issuer signed the data, it has nothing natively about the holder or presenter. This also needs to be verified.
* The verifier also needs to check the content of the attestation messages and any other app-specific criteria. A valid signature means nothing if the mesage content is not as expected.

The actual implementation of presentations is left open-ended. We recommend ;everageing our authentication flow (Sign In with BitBadges) and claims (BitBadges Claims) to be used in conjunction with proof verification / presentation.

* Sign In with BitBadges: Proofs / attestations that are stored in the user's BitBadges account can be revealed / passed along to the authentication provider (the verifier) within the sign-in flow.
* BitBadges Claims: Create a custom plugin or webhook that accepts and verifies attestation(s) from the claiming user when they attempt to claim.&#x20;

These flows natively have protective measures against replay attacks, time windows for verification, and more.

{% content-ref url="../../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../../authenticating-with-bitbadges/)
{% endcontent-ref %}

{% content-ref url="../../../overview/claim-builder/" %}
[claim-builder](../../../overview/claim-builder/)
{% endcontent-ref %}

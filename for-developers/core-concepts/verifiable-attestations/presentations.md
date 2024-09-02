# Presentations

Pre-Readings: [Verifiable Attestations](./)

The last part of attestations is the presentation of the proof to the verifier. With presentations, there are a couple things that need to be considered.

* Proofs / attestations are just cryptographic signatures. If a malicious party gets the signature, the signature will still be valid. Thus, it is important to protect against replay attacks and verify any address ownership as needed.
* While the attestation / proof itself can prove the issuer signed the data, it has nothing natively about the holder or presenter.&#x20;
* The verifier also needs to check the content of the attestation messages and any other app-specific criteria.

The actual implementation of presentations is left open-ended. Consider leverageingour authentication flow (Sign In with BitBadges) and claims (BitBadges Claims) to be used in conjunction with proof verification / presentation.

* Sign In with BitBadges: Proofs / attestations that are stored in the user's BitBadges account can be revealed / passed along to the authentication provider (the verifier).&#x20;
  * You can pass the **expectAttestationsPresentations** variable to the URL query request for your sign-in. This lets the user know that they should attach proofs to their request. You will receive the proofs back in **attestationsPresentations.**&#x20;
  * You can also attach the **onlyProofs** variable to not require any signature from the user (just proofs).
* BitBadges Claims: Create a custom plugin that accepts and verifies attestation(s) from the claiming user.

These flows natively have protective measures against replay attacks, time windows for verification, and more. For a credential / attestation to be valid, the holder must present a) proof of the credential AND b) proof of address ownership.

{% content-ref url="../../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../../authenticating-with-bitbadges/)
{% endcontent-ref %}

{% content-ref url="../../../overview/claim-builder/" %}
[claim-builder](../../../overview/claim-builder/)
{% endcontent-ref %}

**Self-Hosted Support**

Both flows also have an interface to accept custom copy / pasted JSONs. This is useful if you have self-hosted or self-generated an attestation.

**Extensions**

With self-implementations, you may also choose to extend presentations with additional logic, such as wrapping a zero-knowledge proof around the presentation of standard signatures (Bitcoin, Eth, Solana, Cosmos) to only selectively disclose what you want to like we do with BBS+.

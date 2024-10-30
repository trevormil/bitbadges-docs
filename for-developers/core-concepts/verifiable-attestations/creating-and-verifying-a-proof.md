# Verification / Presentations

Pre-Readings: [Verifiable Attestations](./)

Putting it all together, you now need to present the attestation to a verifier when it is needed to be checked.&#x20;

**Presentations**

Presentations are done with the proof interface which should give the verifier everything they need to check everything. We recommend leveraging our authentication flow (Sign In with BitBadges) and claims (BitBadges Claims) to be used in conjunction with proof verification / presentation.

-   Sign In with BitBadges: Proofs / attestations that are stored in the user's BitBadges account can be revealed / passed along to the authentication provider (the verifier) within the sign-in flow.
-   BitBadges Claims: Create a custom plugin or webhook that accepts and verifies attestation(s) from the claiming user when they attempt to claim.&#x20;

These flows natively have protective measures against replay attacks, time windows for verification, and more.

{% content-ref url="../../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../../authenticating-with-bitbadges/)
{% endcontent-ref %}

{% content-ref url="../../../overview/claim-builder/" %}
[claim-builder](../../../overview/claim-builder/)
{% endcontent-ref %}

We do want to note that if you obtain attestations that use BitBadges schemes ('bbs' or 'standard') from these two flows, you can assume the attestations are cryptographically correct. By cryptographically correct, we simply mean that the data integrity proofs / signatures are verified. Nothing else about content, on-chain hashes, etc is guaranteed to be correct. That should be verified on your end.

However, it is always best practice to verify on your end as well (do not trust, verify!).&#x20;

```typescript
import { BitBadgesApi, verifyAttestation } from 'bitbadgesjs-sdk';

//To outsource to server
await BitBadgesApi.verifyAttestation({ attestation });

//To do it locally
await verifyAttestation(attestation);
```

For any non-BitBadges schemes, we do not maintain any message schemas, verifying integrity, etc. This should all be handled on your end.

**Verification**

Once received, the verifier can then check whatever is needed according to the agreed approach:

-   Verifying signatures
-   Verifying on-chain anchors / timestamps
-   Content is well-formed

Additionally, you should consider:

-   If a malicious party gets a cryptographic signature, the signature will still be the same and valid. Thus, it is important to protect against replay attacks, man in the middle attacks, and verify any address ownership as needed. Thus, verification is typically a two-step approach, 1) verify the credential and 2) verify the address of the recipient.
-   While the attestation / proof itself can prove the issuer signed the data, it has nothing natively about the holder or presenter. This also needs to be verified.
-   The verifier also needs to check the content of the attestation messages and any other app-specific criteria. A valid signature means nothing if the mesage content is not as expected.

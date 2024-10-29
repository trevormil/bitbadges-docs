# Deriving a Proof

### **Standard Signatures**

For standard proofs (scheme = 'standard'), selective disclosure is not possible / supported. Simply copy and paste the **dataIntegrityProof** from the attestation exactly as is. No **proofOfIssuance** is needed.

### Alternative Non-Native Approaches

For custom approaches, this is really left open-ended. You may use the **proofOfIssuance** or **dataIntegrityProof** or any part of the interface however you would like.

In alternative approaches like these, BitBadges is just the middleman, so schemas, well-formedness, and other verification is outsourced to the issuer and / or verifier.

### **BBS+ Proofs - Verifying Proof of Issuance**

An important aspect of verifying BBS+ attestations is to verify the link between the "main" issuer and the BBS+ public key. This is done with the **proofOfIssuance** provided. You should verify that the main issuer has given valid approval to use such an approval as issued by themselves. For BitBadges, we use the scheme of the following.

```typescript
'I approve the issuance of attestations signed with BBS+ a5159099a24a8993b5eb8e62d04f6309bbcf360ae03135d42a89b3d94cbc2bc678f68926373b9ded9b8b9a27348bc755177209bf2074caea9a007a6c121655cd4dda5a6618bfc9cb38052d32807c6d5288189913aa76f6d49844c3648d4e6167 as my own.\n\n';
```

We then verify that the signer of the proof of issuance matches the issuer (createdBy) and he key they approved is the BBS key used for the proof.

```typescript
const bbsSigner = body.proofOfIssuance.message.split(' ')[9];
if (bbsSigner !== body.dataIntegrityProof.signer) {
    throw new Error('Proof signer does not match proof of issuance');
}
const address = body.proofOfIssuance.signer;
const chain = getChainForAddress(address);

if (
    convertToBitBadgesAddress(address) !==
    convertToBitBadgesAddress(body.createdBy)
) {
    throw new Error('Signer does not match creator');
}

await getChainDriver(chain).verifySignature(
    address,
    body.proofOfIssuance.message,
    body.proofOfIssuance.signature,
    body.proofOfIssuance.publicKey
);
```

### **BBS+ Proofs - Creation and Verification**

For verifying BBS+ signatures, it is important to note whether you are verifying a derived proof or the original signature. This is determined by **dataIntegrityProof.isDerived.** Typically, we expect the **dataIntegrityProof.signature** to always be a derived proof when using the **iAttestationsProof** interface.&#x20;

Note: A proof can only be derived from the original. You cannot derive a proof from another proof.

To create the proof from the original attestation, the following code can be used. **revealed** is he zero-based indices of the messages that are revealed (i.e. messages elem 0 is revealed = \[0])

We use a generic "nonce" as the nonce because we expect proofs to be verified using an alternative sign-in flow that handles replay attacks there / verification.

<pre class="language-typescript"><code class="lang-typescript">import { createAttestationsProof } from "bitbadgesjs-sdk";

const derivedProof = await createAttestationsProof({
<strong>  signature: Uint8Array.from(Buffer.from(attestation.dataIntegrityProof.signature, 'hex')),
</strong>  publicKey: Uint8Array.from(Buffer.from(attestation.dataIntegrityProof.signer, 'hex')),
  messages: attestation.messages.map((message) => Uint8Array.from(Buffer.from(message, 'utf-8'))),
  nonce: Uint8Array.from(Buffer.from('nonce', 'utf8')),
  revealed: attestation.messages
    .map((_, idx) => (proof.messages.includes(attestation.messages[idx]) ? idx : -1))
    .filter((x) => x !== -1)
});

setProof(
  new AttestationsProof({
    ...proof,
    dataIntegrityProof: {
      signature: Buffer.from(derivedProof).toString('hex'),
      signer: attestation.dataIntegrityProof.signer,
      isDerived: true
    }
  })
);
</code></pre>

To verify the original, you need all N messages and will use blsVerify. To verify a derived proof, you only need to know the messages used to derive the proof.

```tsx
import { verifyAttestationsPresentationSignatures } from 'bitbadgesjs-sdk';

const isDerivedProof = true;
await verifyAttestationsPresentationSignatures(proof, isDerivedProof);
```

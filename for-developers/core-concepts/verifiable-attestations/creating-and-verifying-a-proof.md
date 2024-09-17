# Creating and Verifying a Proof

Pre-Readings: [Verifiable Attestations](./)

**Proofs vs Core Attestations**

As a holder, you are expected to generate proof(s) to be disclosed to verifiers rather than disclosing the original attestation with all details. A proof may only reveal a subset of the messages in the original attestation (or oftentimes, it may include all of the messages). Proofs may also optionally have different metadata.In many cases, the proof will be basically the same as the original.

There are user interfaces for handling this all on the frontend. However, below, we go into detail for how you can do it yourself. Check out [https://bitbadges.io/attestations/proofgen](https://bitbadges.io/attestations/proofgen) for a helper tool for generating BBS+ signatures.

```typescript
export interface iAttestationsProof<T extends NumberType> {
    createdBy: string;
    scheme: 'bbs' | 'standard';

    attestationMessages: string[];

    dataIntegrityProof: {
        signature: string;
        signer: string;
        publicKey?: string;
    };

    proofOfIssuance: {
        message: string;
        signer: string;
        signature: string;
        publicKey?: string;
    };

    name: string;
    image: string;
    description: string;

    entropies?: string[];
    updateHistory?: iUpdateHistory<T>[];
    anchors?: {
        txHash?: string;
        message?: string;
    }[];
}
```

### Custom Logic

It is important to note that proof verification is not limited to that is provided in the interface, you will typically also need to check the attestation messages are as expected against other private values (e.g. matching attestation data to a user). This is application-specific, but we expect you to handle everything for proper verification.

**Inherent Trust for the Issuer**

All attestations / credentials inherently get their credibility from the issuer, so there is already a bit of trust there. However, additional measures can be taken to protect against a malicious issuer. Some examples include:

-   On-chain ID -> data integrity maps to prevent issuer from issuing duplicates (each credential ID can only correspond to one credential)
-   Anchors / Data Commitments - The issuer or holder can commit to proof of knowledge on-chain at some point which can be verified later. This gives a verifiable timestamp for when the data was known by. See below for more info.

### On-Chain Anchors + Update History

While the cryptographic signature prove data integrity / proof of knowledge, you may also need to verify knowledge or integrity with timestamps. For this, consider creating on-chain anchor transactions which can be used for such purposes.

Anchors do not necessarily have to reveal private information. Posting a hash, encryption, etc is sufficient as long as it can prove what you need it to prove.

For BitBadges, anchors are facilitated through the x/anchor module (MsgAddCustomData). It is a very simple module that allows you to store arbitrary strings.&#x20;

If an anchor is created through the BitBadges site, we use the following algorithm. If we have N attestation messages, we also have N entropies. We can then post the SHA256 hash of (message + entropy) on-chain. When revealed to a verifier in a proof, they can verify that data integrity has been maintained if they have the palintext message + entropy value. The random hash posted on-chain reveals nothing confidential but provides a verifiable timestamp for the data. This is facilitated through the x/anchor module's MsgAddCustomData.

```typescript
{
    type: 'MsgAddCustomData',
    msg: {
      data: JSON.stringify(
        attestation.attestationMessages.map((message, idx) => {
          return CryptoJS.SHA256(message + entropies[idx]).toString();
        })
      )
    }
}
```

Update history is also maintained by BItBadges in a centralized manner, but anchors could be useful to provide additional information to verifiers about when the data changed, verifiable timestamps, etc.

### **Standard Proofs**

For standard proofs, selective disclosure is not possible / supported. Simply copy and paste the **dataIntegrityProof** from the attestation exactly as is.

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

For verifying BBS+ signatures, it is important to note whether you are verifying a derived proof or the original signature.

We expect the **dataIntegrityProof.signature** to always be a derived proof when using the **iAttestationsProof** interface. However, the **proofOfIssuance** may have the original.

To create the proof from the original attestation, the following code can be used. **revealed** is he zero-based indices of the messages that are revealed (i.e. messages elem 0 is revealed = \[0])

We use a generic "nonce" as the nonce because we expect proofs to be verified using an alternative sign-in flow that handles replay attacks there.

<pre class="language-typescript"><code class="lang-typescript">import { createAttestationsProof } from "bitbadgesjs-sdk";

const derivedProof = await createAttestationsProof({
<strong>  signature: Uint8Array.from(Buffer.from(attestation.dataIntegrityProof.signature, 'hex')),
</strong>  publicKey: Uint8Array.from(Buffer.from(attestation.dataIntegrityProof.signer, 'hex')),
  messages: attestation.attestationMessages.map((message) => Uint8Array.from(Buffer.from(message, 'utf-8'))),
  nonce: Uint8Array.from(Buffer.from('nonce', 'utf8')),
  revealed: attestation.attestationMessages
    .map((_, idx) => (proof.attestationMessages.includes(attestation.attestationMessages[idx]) ? idx : -1))
    .filter((x) => x !== -1)
});

setProof(
  new AttestationsProof({
    ...proof,
    dataIntegrityProof: {
      signature: Buffer.from(derivedProof).toString('hex'),
      signer: attestation.dataIntegrityProof.signer
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

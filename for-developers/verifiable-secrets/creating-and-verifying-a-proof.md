# Creating and Verifying a Proof

Pre-Readings: [Verifiable Secrets](./)

As a holder, you are expected to generate proof(s) to be disclosed to verifiers via the following the interface.

```typescript
export interface iSecretsProof<T extends NumberType> {
  createdBy: string;
  scheme: 'bbs' | 'standard';

  secretMessages: string[];
  
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

  entropies: string[];
  updateHistory: iUpdateHistory<T>[];
  anchors: {
    txHash?: string;
    message?: string;
  }[];
}
```

### Custom Logic

It is important to note that proof verification is not limited to that is provided in the interface. you will typically need to check the secret messages against other private values (e.g. matching secret data to a user). This is application-specific, but we expect you to handle everything for proper verification.

**Inherent Trust for the Issuer**

All secrets / credentials inherently get their credibility from the issuer, so there is already a bit of trust there. However, additional measures can be taken to protect against a malicious issuer. Some examples include:

* On-chain ID -> data integrity maps to prevent issuer from issuing duplicates (each credential ID can only correspond to one credential)
* Anchors / Data Commitments - The issuer or holder can commit to proof of knowledge on-chain at some point which can be verified later. This gives a verifiable timestamp for when the data was known by. See below for more info.

### On-Chain Anchors + Update History

There are a couple fields here which are used to prove data integrity and proof of knowledge. These are not necessary for all use cases.

Anchors are proof that the credential was known / issued by a certain time. These should not reveal any confidential information publicly.&#x20;

If an anchor is created through the BitBadges site, we use the following algorithm. If we have N secret messages, we also have N entropies. We can then post the SHA256 hash of (message + entropy) on-chain. When revealed to a verifier in a proof, they can verify that data integrity has been maintained if they have the palintext message + entropy value. The random hash posted on-chain reveals nothing confidential but provides a verifiable timestamp for the data. This is facilitated through the x/anchor module's MsgAddCustomData.

```typescript
{
    type: 'MsgAddCustomData',
    msg: {
      data: JSON.stringify(
        secret.secretMessages.map((message, idx) => {
          return CryptoJS.SHA256(message + entropies[idx]).toString();
        })
      )
    }
}
```



Update history is maintained by BItBadges in a centralized manner, but it could be useful to provide additional information to verifiers about when the data changed, etc.

### **Standard Proofs**

For standard proofs, selective disclosure is not possible / supported. Simply copy and paste the **dataIntegrityProof** from the secret exactly as is.&#x20;

### **BBS+ Proofs - Verifying Proof of Issuance**

An important aspect of verifying BBS+ secrets is to verify the link between the "main" issuer and the BBS+ public key. This is done with the **proofOfIssuance** provided. You should verify that the main issuer has given valid approval to use such an approval as issued by themselves.

### **BBS+ Proofs - Creation and Verification**

For verifying BBS+ signatures, it is important to note whether you are verifying a derived proof or the original signature.&#x20;

We expect the **dataIntegrityProof.signature** to always be a derived proof when using the **iSecretsProof** interface. However, the **proofOfIssuance** may have the original.

To create the proof from the original secret, the following code can be used. **revealed** is he zero-based indices of the messages that are revealed (i.e. messages elem 0 is revealed = \[0])

&#x20;We use a generic "nonce" as the nonce because we expect proofs to be verified using an alternative sign-in flow that handles replay attacks there.

```typescript
const derivedProof = await blsCreateProof({
  signature: Uint8Array.from(Buffer.from(secret.dataIntegrityProof.signature, 'hex')),
  publicKey: Uint8Array.from(Buffer.from(secret.dataIntegrityProof.signer, 'hex')),
  messages: secret.secretMessages.map((message) => Uint8Array.from(Buffer.from(message, 'utf-8'))),
  nonce: Uint8Array.from(Buffer.from('nonce', 'utf8')),
  revealed: secret.secretMessages
    .map((_, idx) => (proof.secretMessages.includes(secret.secretMessages[idx]) ? idx : -1))
    .filter((x) => x !== -1)
});

setProof(
  new SecretsProof({
    ...proof,
    dataIntegrityProof: {
      signature: Buffer.from(derivedProof).toString('hex'),
      signer: secret.dataIntegrityProof.signer
    }
  })
);
```

To verify the original, you need all N messages and will use blsVerify. To verify a derived proof, you only need to know the messages used to derive the proof. You will use blsVerifyProof for this.

<pre class="language-typescript"><code class="lang-typescript">import { blsVerify, blsVerifyProof } from '@mattrglobal/bbs-signatures';
<strong>
</strong><strong>if (!derivedProof) {
</strong>  const isProofVerified = await blsVerify({
    signature: Uint8Array.from(Buffer.from(body.dataIntegrityProof.signature, 'hex')),
    publicKey: Uint8Array.from(Buffer.from(body.dataIntegrityProof.signer, 'hex')),
    messages: body.secretMessages.map((message) => Uint8Array.from(Buffer.from(message, 'utf-8')))
  });

  if (!isProofVerified.verified) {
    throw new Error('Data integrity proof not verified');
  }
} else {
  const isProofVerified = await blsVerifyProof({
    proof: Uint8Array.from(Buffer.from(body.dataIntegrityProof.signature, 'hex')),
    publicKey: Uint8Array.from(Buffer.from(body.dataIntegrityProof.signer, 'hex')),
    messages: body.secretMessages.map((message) => Uint8Array.from(Buffer.from(message, 'utf-8'))),
    nonce: Uint8Array.from(Buffer.from('nonce', 'utf8'))
  });

  if (!isProofVerified.verified) {
    throw new Error('Data integrity proof not verified');
  }
}
</code></pre>

&#x20;

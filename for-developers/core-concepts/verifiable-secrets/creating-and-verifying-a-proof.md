# Creating and Verifying a Proof

Pre-Readings: [Verifiable Secrets](./)

As a holder, you are expected to generate proof(s) to be disclosed to verifiers rather than disclosing the original secret with all details.&#x20;

There are user interfaces for handling this all on the frontend which should be adequate for almost all use cases. However, below, we go into detail for how you can do it yourself.

Check out [https://bitbadges.io/secrets/proofgen](https://bitbadges.io/secrets/proofgen) for a helper tool for generating BBS+ signatures.

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

    entropies?: string[];
    updateHistory?: iUpdateHistory<T>[];
    anchors?: {
        txHash?: string;
        message?: string;
    }[];
}
```

### Custom Logic

It is important to note that proof verification is not limited to that is provided in the interface. you will typically need to check the secret messages against other private values (e.g. matching secret data to a user). This is application-specific, but we expect you to handle everything for proper verification.

**Inherent Trust for the Issuer**

All secrets / credentials inherently get their credibility from the issuer, so there is already a bit of trust there. However, additional measures can be taken to protect against a malicious issuer. Some examples include:

-   On-chain ID -> data integrity maps to prevent issuer from issuing duplicates (each credential ID can only correspond to one credential)
-   Anchors / Data Commitments - The issuer or holder can commit to proof of knowledge on-chain at some point which can be verified later. This gives a verifiable timestamp for when the data was known by. See below for more info.

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

An important aspect of verifying BBS+ secrets is to verify the link between the "main" issuer and the BBS+ public key. This is done with the **proofOfIssuance** provided. You should verify that the main issuer has given valid approval to use such an approval as issued by themselves. For BitBadges, we use the scheme of the following.

```typescript
'I approve the issuance of secrets signed with BBS+ a5159099a24a8993b5eb8e62d04f6309bbcf360ae03135d42a89b3d94cbc2bc678f68926373b9ded9b8b9a27348bc755177209bf2074caea9a007a6c121655cd4dda5a6618bfc9cb38052d32807c6d5288189913aa76f6d49844c3648d4e6167 as my own.\n\n';
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
    convertToCosmosAddress(address) !== convertToCosmosAddress(body.createdBy)
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

For verifying BBS+ signatures, it is important to note whether you are verifying a derived proof or the original signature.&#x20;

We expect the **dataIntegrityProof.signature** to always be a derived proof when using the **iSecretsProof** interface. However, the **proofOfIssuance** may have the original.

To create the proof from the original secret, the following code can be used. **revealed** is he zero-based indices of the messages that are revealed (i.e. messages elem 0 is revealed = \[0])

&#x20;We use a generic "nonce" as the nonce because we expect proofs to be verified using an alternative sign-in flow that handles replay attacks there.

<pre class="language-typescript"><code class="lang-typescript">import { createSecretsProof } from "bitbadgesjs-sdk";

const derivedProof = await createSecretsProof({
<strong>  signature: Uint8Array.from(Buffer.from(secret.dataIntegrityProof.signature, 'hex')),
</strong>  publicKey: Uint8Array.from(Buffer.from(secret.dataIntegrityProof.signer, 'hex')),
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
</code></pre>

To verify the original, you need all N messages and will use blsVerify. To verify a derived proof, you only need to know the messages used to derive the proof.

```tsx
import { verifysecretsPresentationsignatures } from 'bitbadgesjs-sdk';

const isDerivedProof = true;
await verifysecretsPresentationsignatures(proof, isDerivedProof);
```

# Creating a Attestation

Pre-Readings: [Verifiable Attestations](./)

On the official site, we provide interfaces to create attestations. However, you can also self-generate locally and upload via the BitBadges API as well. Below, we provide information on how it works behind the scenes.&#x20;

<figure><img src="../../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

The creation interface is as follows. All attestations are a series of one or more **attestationMessages** which can be either in 'json' or 'plaintext' **messageFormat**. The schema of the message is left up to the issuer.

```typescript
export interface CreateAttestationPayload {
    proofOfIssuance: {
        message: string;
        signature: string;
        signer: string;
        publicKey?: string;
    };

    scheme: 'bbs' | 'standard';
    messageFormat: 'json' | 'plaintext';
    attestationMessages: string[];
    dataIntegrityProof: {
        signature: string;
        signer: string;
        publicKey?: string;
    };

    name: string;
    image: string;
    description: string;
}
```

### **Schemas**

Like mentioned above, we leave the schema system up to the issuer. The only thing we store about the schema is the **messageFormat** = 'json' or 'plaintext'. Feel free to use existing models such as the [W3C Verifiable Credentials](https://www.w3.org/TR/vc-data-model-2.0/) model, or create your own.

You may also choose to reuse some data on the BitBadges or other blockchains as well (ex: must own this badge ID or issuer = manager of this collection or revocation = is this asset burned?). It is up to you how to define this in your schema.

If you are pointing to on-chain data, you should also consider privacy concerns. All data on the blockchain is public, so for example, you may not want to use on-chain revocations through badge transfers because anyone can see when/if that badge was revoked. Or, if you do use it, you may consider using privacy-preserving techniques (indistinguishable metadata, one-time use only addresses) so that nothing unintended can be learned.

Example W3C Verifiable Credentials

```json
{
    "@context": [
        "https://www.w3.org/ns/credentials/v2",
        "https://www.w3.org/ns/credentials/examples/v2"
    ],
    "id": "http://license.example/credentials/9837",
    "type": ["VerifiableCredential", "ExampleDrivingLicenseCredential"],
    "issuer": "https://license.example/issuers/48",
    "validFrom": "2020-03-14T12:10:42Z",
    "credentialSubject": {
        "id": "did:example:f1c276e12ec21ebfeb1f712ebc6",
        "license": {
            "type": "ExampleDrivingLicense",
            "name": "License to Drive a Car"
        }
    },
    "credentialStatus": [
        {
            "id": "https://license.example/credentials/status/84#14278",
            "type": "BitstringStatusListEntry",
            "statusPurpose": "revocation",
            "statusListIndex": "14278",
            "statusListCredential": "https://license.example/credentials/status/84"
        },
        {
            "id": "https://license.example/credentials/status/84#82938",
            "type": "BitstringStatusListEntry",
            "statusPurpose": "suspension",
            "statusListIndex": "82938",
            "statusListCredential": "https://license.example/credentials/status/84"
        }
    ]
}
```

**Data Integrity**

The integrity of the data is committed to by a cryptographic signature as we will show below. This proves to any potential verifier that the issuer did indeed issue this attestation because a cryptographic signature cannot be forged. The signatures are stored by the holders along with the credential and presented to verifiers.

In a a system like this, typically, the attestation gains its credibility from the issuer, so there is inherently some trust there. However, you may also opt to use additional protection measures against a malicious issuer. For example:

-   On-chain map of ID -> signatures to protect against an issuer issuing duplicate IDs
-   On-chain map of badge ID -> attestation IDs to protect against duplicate attestations issued per badge
-   Anchor the signature on-chain to prove existence by a certain time and to maintain data integrity (more on this in the next page with anchors)

### Standard Signatures

For standard signatures (**scheme** = 'standard'), you can sign 1 message (so **attestationMessages**.length = 1), and the **dataIntegrityProof** will be the resulting signature of the only message from your main key pair supported natively by BitBadges. See the signing transaction flow for more information on manually implementing signatures. No **proofOfIssuance** is required (can be left blank).

```typescript
const sig = await chain.signChallenge(attestation.attestationMessages[0]);
const pubKey = await chain.getPublicKey(chain.address);

body = {
    ...attestation,
    dataIntegrityProof: {
        signer: chain.address,
        signature: sig.signature,
        publicKey: pubKey,
    },
    proofOfIssuance: {
        signature: '',
        signer: '',
        message: '',
    },
};
```

### **BBS+**

For BBS+ signatures (**scheme** = 'bbs'), you can sign N **attestationMessages** and the **dataIntegrityProof** will be the BBS signature of those N message. On the BitBadges site, all BBS+ key pairs are one-time use only. The key pair is generated, signs the transaction, and then is discarded because it is never needed again.

<pre class="language-typescript"><code class="lang-typescript">import { BlsKeyPair, blsSign, generateBls12381G2KeyPair } from '@mattrglobal/bbs-signatures';

<strong>const signature = await blsSign({
</strong>  keyPair: keyPair!,
  messages: attestation.attestationMessages.map((message) => Uint8Array.from(Buffer.from(message, 'utf-8')))
});

setAttestation((prev) => ({
  ...prev,
  dataIntegrityProof: {
<strong>    signer: Buffer.from(keyPair?.publicKey ?? '').toString('hex'),
</strong>    signature: Buffer.from(signature).toString('hex')
  }
}));
</code></pre>

Because BBS+ are not actually signed by your "main" address, we also require a **proofOfIssuance** to establish this link.

```typescript
const message = `I approve the issuance of credentials signed with BBS+ ${attestation.dataIntegrityProof.signer} as my own.\n\n`;
const sig = await chain.signChallenge(message);
const publicKey = await chain.getPublicKey(chain.bitbadgesAddress);
body = {
    ...attestation,
    proofOfIssuance: {
        message,
        signer: chain.address,
        signature: sig.signature,
        publicKey,
    },
};
```

### **Public Keys**

Public keys are currently only needed to be provided for Cosmos signatures in order to verify. Otherwise, you can leave them blank.

### **Metadata**

Each attestation has metadata attached to it for display purposes (i.e. name, image, description). This metadata should be considered public and viewable by all parties (issuer, verifier, and holder).

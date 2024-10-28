# Creating an Attestation

Pre-Readings: [Verifiable Attestations](./)

On the official site, we provide interfaces to create attestations (Create -> Attestations).

You can also self-generate locally and upload via the BitBadges API as well. Below, we provide information on how it works behind the scenes.

<figure><img src="../../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

The creation interface is as follows. All attestations are a series of one or more **attestationMessages** which can be either in 'json' or 'plaintext' **messageFormat**. You can use the corresponding API endpoint to create programmatically.

```typescript
await BitBadgesApi.createAttestation(...)
await BitBadgesApi.updateAttestation(...)
await BitBadgesApi.createAttestationProof(...)
await BitBadgesApi.updateAttestationProof(...)
```

```typescript
export interface CreateAttestationPayload {
  /**
   * Proof of issuance is used for BBS+ signatures (scheme = bbs) only.
   * BBS+ signatures are signed with a BBS+ key pair, but you would often want the issuer to be a native address.
   * The prooofOfIssuance establishes a link saying that "I am the issuer of this attestation signed with BBS+ key pair ___".
   *
   * Fields can be left blank for standard signatures.
   */
  proofOfIssuance: {
    message: string;
    signature: string;
    signer: string;
    publicKey?: string;
  };

  /** The message format of the attestationMessages. */
  messageFormat: 'plaintext' | 'json';
  /**
   * The scheme of the attestation. BBS+ signatures are supported and can be used where selective disclosure is a requirement.
   * Otherwise, you can simply use your native blockchain's signature scheme.
   */
  scheme: 'bbs' | 'standard' | 'custom' | string;

  /** The original provider of the attestation. Used for third-party attestation providers. */
  originalProvider?: string;

  /** The type of the attestation (e.g. credential). */
  type: string;
  /**
   * Thesse are the attestations that are signed.
   * For BBS+ signatures, there can be >1 attestationMessages, and the signer can selectively disclose the attestations.
   * For standard signatures, there is only 1 attestationMessage.
   */
  attestationMessages: string[];

  /**
   * This is the signature and accompanying details of the attestationMessages. The siganture maintains the integrity of the attestationMessages.
   *
   * This should match the expected scheme. For example, if the scheme is BBS+, the signature should be a BBS+ signature and signer should be a BBS+ public key.
   */
  dataIntegrityProof: {
    signature: string;
    signer: string;
    publicKey?: string;
  };

  /** Metadata for the attestation for display purposes. Note this should not contain anything sensitive. It may be displayed to verifiers. */
  name: string;
  /** Metadata for the attestation for display purposes. Note this should not contain anything sensitive. It may be displayed to verifiers. */
  image: string;
  /** Metadata for the attestation for display purposes. Note this should not contain anything sensitive. It may be displayed to verifiers. */
  description: string;
}

```

### **Schemes / Expected Formats**

The **messageFormat** = 'json' or 'plaintext' will determine what format the messages should be in.

The **scheme** field will determine a lot about the attestation:

* 'bbs': The N **attestationMessages** must be signed using the BBS+ signature algorithm. A proof of issuance is also required. The schema of the message is left up to the issuer. See below.
* 'standard': Only 1 attestation message is expected. The message should be signed using a standard wallet signature. The schema of the message is left up to the issuer. See below.
* 'custom' or anything else: If you do not want to use a BitBadges native scheme, you can also simply add your own. Feel free to use existing models such as the [W3C Verifiable Credentials](https://www.w3.org/TR/vc-data-model-2.0/) model, create your own, or anything else.

More can be found in the individual scheme documentation later in this section.

### Creating / Verifying Signatures

Note: When received from the BitBadges API directly, you can assume that signatures are already correct for non-custom implementations (bb or standard). However, it is always best practice to verify them on your end as well.

Public keys are currently only needed to be provided for Cosmos signatures in order to verify. Otherwise, you can leave them blank.

#### Standard Signatures

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

#### **BBS+ Signatures**

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

### Alternative Schemes

Note that non-BitBadges native schemes / approaches may have different verification algorithms. Please make sure your approach follows the verification approach.

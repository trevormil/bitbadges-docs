# Creating a Secret

Pre-Readings: [Verifiable Secrets](./)

On the official site, we provide interfaces to create secrets. This should be adequate for almost all use cases. However, you can self-generate locally and upload via the BitBadges API as well.&#x20;

The creation interface is as follows.

```typescript
export interface CreateSecretRouteRequestBody {
  proofOfIssuance: {
    message: string;
    signature: string;
    signer: string;
    publicKey?: string;
  };

  scheme: 'bbs' | 'standard';
  secretMessages: string[];
  dataIntegrityProof: {
    signature: string;
    signer: string;
    publicKey?: string;
  };

  type: string;
  
  name: string;
  image: string;
  description: string;
}
```

### Standard Signatures

For standard signatures (**scheme** = 'standard'), you can sign 1 message (so **secretMessages**.length = 1), and the **dataIntegrityProof** will be the resulting signature of the only message from your main key pair supported natively by BitBadges. No **proofOfIssuance** is required (can be left blank).

```typescript
const sig = await chain.signChallenge(secret.secretMessages[0]);
const pubKey = await chain.getPublicKey(chain.address);

body = {
  ...secret,
  dataIntegrityProof: {
    signer: chain.address,
    signature: sig.signature,
    publicKey: pubKey
  },
  proofOfIssuance: {
    signature: '',
    signer: '',
    message: ''
  }
};
```

### **BBS+**

For BBS+ signatures (**scheme** = 'bbs'), you can sign N **secretMessages** and the **dataIntegrityProof** will be the BBS signature of those N message. See [https://github.com/mattrglobal/bbs-signatures](https://github.com/mattrglobal/bbs-signatures) for the code to create the signatures.&#x20;

On the BitBadges site, all BBS+ key pairs are one-time use only. The key pair is generated, signs the transaction, and then is discarded because it is never needed again.

<pre class="language-typescript"><code class="lang-typescript">import { BlsKeyPair, blsSign, generateBls12381G2KeyPair } from '@mattrglobal/bbs-signatures';

<strong>const signature = await blsSign({
</strong>  keyPair: keyPair!,
  messages: secret.secretMessages.map((message) => Uint8Array.from(Buffer.from(message, 'utf-8')))
});

setSecret((prev) => ({
  ...prev,
  dataIntegrityProof: {
    signer: Buffer.from(keyPair?.publicKey ?? '').toString('hex'),
    signature: Buffer.from(signature).toString('hex')
  }
}));
</code></pre>

Because BBS+ are not actually signed by your "main" address, we also require a **proofOfIssuance** to establish this link.&#x20;

```typescript
const message = `I approve the issuance of credentials signed with BBS+ ${secret.dataIntegrityProof.signer} as my own.\n\n`;
const sig = await chain.signChallenge(message);
const publicKey = await chain.getPublicKey(chain.cosmosAddress);
body = {
  ...secret,
  proofOfIssuance: {
    message,
    signer: chain.address,
    signature: sig.signature,
    publicKey
  }
};
```

### **Public Keys**

Public keys are currently only needed to be provided for Cosmos signatures in order to verify. Otherwise, you can leave them blank.

### **Metadata**

Each secret has metadata attached to it for display purposes (i.e. name, image, description). This metadata should be considered public and viewable by all parties (issuer, verifier, and holder).

### **Types**

Types are just used for querying purposes. Typically, this will be 'credential'. For now, we do not do anything special with types.

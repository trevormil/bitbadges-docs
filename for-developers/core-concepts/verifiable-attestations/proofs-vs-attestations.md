# Proofs vs Attestations

Attestations are the original document from which different proofs can be derived from. Attestations will always remain in your account, whereas the proof interface is used when giving information to verifiers, displaying on your profile, and so on. Proofs follow a similar interface plus/minus a few fields, but proofs may change the following details, depending on the implementation:

* May have different metadata
* Can selectively disclose certain information but not others (for approaches that support selective disclosure).

In many cases though, the proof will be the same as the original.&#x20;

There are user interfaces for handling this all on the frontend. However, below, we go into detail for how you can do it yourself. Check out [https://bitbadges.io/attestations/proofgen](https://bitbadges.io/attestations/proofgen) for a helper tool for generating BBS+ signatures.

```typescript
export interface iAttestationsProof<T extends NumberType> {
    createdBy: string;
    scheme: 'bbs' | 'standard' | string;
    originalProvider: string;

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

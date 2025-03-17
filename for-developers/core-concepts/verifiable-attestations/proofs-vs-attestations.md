# Proofs vs Attestations

Attestations are the original document from which different proofs can be derived from, depending on the selected scheme. For example, for BBS signatures, you can selectively reveal M of N messages.

```
Proofs are a derivation from the core attestation with a few changes.
```

Proofs follow a very similar interface plus/minus a few fields, but proofs may change the following details, depending on the implementation:

* May have different metadata or visibility properties
* Can selectively disclose certain information but not others (for approaches that support selective disclosure).

There are user interfaces for handling this all on the frontend. However, below, we go into detail for how you can do it yourself. Check out [https://bitbadges.io/attestations/proofgen](https://bitbadges.io/attestations/proofgen) for a helper tool for generating BBS+ signatures.

Most notably, for BBS derived proofs, the dataIntegrityProof.isDerived will be true.

```typescript
export interface iAttestationsProof<T extends NumberType> {
    createdBy: string;
    scheme: 'bbs' | 'standard' | string;
    originalProvider: string;

    messages: string[];

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

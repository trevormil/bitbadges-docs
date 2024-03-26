# ZK Proofs

Zero knowledge proofs are also supported in the approval interface. If you provide a valid proof, you are granted approval.&#x20;

Pre-Readings: [https://docs.circom.io/background/background/](https://docs.circom.io/background/background/) and have a basic understanding of the concepts of zkSnarks and ZK Proofs from a high level. You do not need to fully know the math / cryptography behind the scenes to write ZK circuits, but you should be familiar with the main concepts.

**Replays**

Each valid proof solution can only be used once. This is tracked with the challenge tracker ID, in the same manner as merkle challenges (we refer you there for more info).

Note that a ZK Proof can have many valid solutions. Design your proofs with this in consideration.

**Storage**

On the blockchain, we store an array of proofs that need to be solved for each approval (if array is empty, there is no need). Users must solve all the proofs in the array for approval. This is stored in the approval's **approvalCriteria.zkProofs.** See the iZkProof interface below. All that we store on-chain (for scalability reasons) is the verification key. You may provide additional details (such as the circuit, proving key, etc) via the uri or customData.&#x20;

```typescript
export interface iZkProof {
  /**
   * The verification key of the zkProof.
   */
  verificationKey: string;

  /**
   * The URI where to fetch the zkProof metadata from.
   */
  uri: string;

  /**
   * Arbitrary custom data that can be stored on-chain.
   */
  customData: string;
}

```

**Proof Solutions**

Within MsgTransferBadges, users can specify **zkProofSolutions** array where they provide the valid solutions to the proofs. These follow the interface as seen below.

```
export interface iZkProofSolution {
  /**
   * The proof of the zkProof.
   */
  proof: string;

  /**
   * The public inputs of the zkProof.
   */
  publicInputs: string;
}
```

**Formatting**

We rely heavily on [Circom implementation](https://docs.circom.io/getting-started/installation/) for proofs and formatting. Circom is a library and full programming language to write ZK proofs.&#x20;

Proving Method: zkSnark Groth16

```
pragma circom 2.0.0;

/*This circuit template checks that c is the multiplication of a and b.*/  

template Multiplier2 () {  

   // Declaration of signals.  
   signal input a;  
   signal input b;  
   signal output c;  

   // Constraints.  
   c <== a * b;  
}
```

With every compiled Circom circuit + valid proof, you will get the following files:

* verification\_key.json - The verification key for proving the circuit
* proof.json - A succint proof proving all criteria is satisfied
* public.json - The public inputs to the proof.

These JSON files should be stringified, and they are what is passed on-chain via storage and the solutions.

The verificationKey.json is what you will pass into verificationKey field of iZkProof.

The proof.json is what you will pass into proof of iZkProofSolution.

The public.json is what you will pass into publicInputs of iZkProofSolution.



Examples

Create Collection Msg w/ Proof - [https://github.com/BitBadges/bitbadges-indexer/blob/master/src/setup/bootstrapped-collections/19\_zkp.json](https://github.com/BitBadges/bitbadges-indexer/blob/master/src/setup/bootstrapped-collections/19\_zkp.json)

Sample Transfer Msg - [https://github.com/BitBadges/bitbadges-indexer/tree/master/src/setup/helpers](https://github.com/BitBadges/bitbadges-indexer/tree/master/src/setup/helpers)

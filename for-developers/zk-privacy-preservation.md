# 🕵️ ZK / Privacy Preservation

While all badge collections are public and created on the BitBadges blockchain, we recognize privacy is an important aspect in many cases. Here are some ways BitBadges can be used in a privacy-preserving context.

### **Verifiable Off-Chain Secrets**

**Proof of Knowledge**

Certain information can be kept secret off-chain, but the commitment to that information can be on-chain somewhere (e.g. post the hash on-chain but keep the preimage secret). This can be leveraged to prove authenticity in certain use cases.&#x20;

Check out the x/anchor module for anchoring arbitrary data with timestamps. Or, it can be anchored directly in a collection w/ the customData fields.

**Proof of Knowledge for Approval**

Certain secrets may even be configured to perform on-chain actions (e.g. solve a zero-knowledge proof for approval or insert a secret code for approval). Some actions are natively supported (ZK Proofs, codes, etc), or you can self implement with CosmWasm.

### Privatizing Public Details

Some details are simply always public on the blockchain, but you can configure them in a way that does not leak information.

**Blank Metadata**

You could create your collection to be generic with a default placeholder metadata. Balances will still be public, but the collections' purpose will not be public.&#x20;

**One-Time Use Only Addresses**

You can also create / issue a collection with a unique one-time use only burner address. Or, in a similar manner, you can receive a badge with a unique, one-time use burner address that isn't linked with anything else.

**Gated / Hidden Balances**

We are also working on a way to keep balances gated / hidden instead of alwasy being public.

### **Verifiable Credentials**

[Verifiable Credentials](https://www.w3.org/TR/vc-data-model-2.0/) are growing in popularity, especially in the blockchain space. They provide a mechanism to express [credentials](https://www.w3.org/TR/vc-data-model-2.0/#dfn-credential) on the Web in a way that is cryptographically secure, privacy respecting, and machine-verifiable.

While BitBadges and VCs have similar use cases, they can be used together in a complementary manner.&#x20;

* Create a badge and link it with a VC (either through the badge metadata or standards or custom data). To "have the credential", you must own the badge and prove the credential. This can be used in cases where the credential itself can be public and is to be displayed in a portfolio (BitBadges) but has certain aspects that may need to maintain prviate (VCs). This also enables VCs to be transferred and owned.&#x20;
  * For example, you may want a generic diploma badge in your BitBadges portfolio, but the GPA, date of graduation, etc should be private. With VCs, you can still attest to the private information to whoever needs it like employers.
* Use the BitBadges token standard to clearly define revocation / suspension rights. Maybe the entire credential should be private, but you can use an on-chain badge's status for the revocation part of a VC. For example, if the badge is burned, it is considered revoked.

We are working on formalizing the standards to use BitBadges and VCs together.

### **Zero Knowledge Proof of Ownership**

In some cases, you may want to prove you meet criteria without revealing your identity (e.g. own the over age 21 badge but don't reveal your name, etc)

Given a snapshot of a collection's balances or list of addresses that is agreed upon between a prover and a verifier, the prover can prove with a zero-knowledge proof that they own the private key to an address in the snapshot without revealing the private key.

Note this requires proving the signature within the zero-knowledge proof as well (e.g. [here](https://ethresear.ch/t/efficient-ecdsa-signature-verification-using-circom/13629)). Certain signature algorithms probably aren't implemented in zero-knowledge formats yet (BIP322), so the implementation would have to take that into account.

Resources: [https://github.com/personaelabs/spartan-ecdsa](https://github.com/personaelabs/spartan-ecdsa), [https://github.com/Electron-Labs/ed25519-circom](https://github.com/Electron-Labs/ed25519-circom)

Extensions: Supporting >=X amount owned, ownership times, and more

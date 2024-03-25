# 🕵️ ZK / Privacy Preservation

While all badges are public on the BitBadges blockchain, we recognize privacy is an important aspect in many cases. Here are some ways BitBadges can be used in a privacy-preserving context.

**Zero Knowledge Proof of Ownership**

Given a snapshot of a collection's balances that is agreed upon between a prover and a verifier, the prover can prove with a zero-knowledge proof that they own the private key to an address who owns X amount of badges in the snapshot without revealing the private key.

Tutorial: TODO

**Verifiable Off-Chain Secrets**

Certain information can be kept secret off-chain, but the commitment to that information can be on-chain somewhere (e.g. post the hash on-chain but keep the preimage secret). This can be leveraged to prove authenticity of certain things.&#x20;

Check out the x/anchor module for anchoring arbitrary data with timestamps. Or, it can be anchored directly in a collection w/ the customData fields.

Certain secrets may even be configured to perform on-chain actions (e.g. solve a zero-knowledge proof for approval or insert a secret code for approval). Some actions are natively supported (see the approval criteria), or you can self implement with CosmWasm.

**Blank Metadata**

You could create your collection to be generic and placeholder metadata. Balances will still be public, but the collections' purpose will not be public.&#x20;

**One-Time Use Only Addresses**

You can also create / issue a collection with a unique one-time use only burner address.

Or, in a similar manner, you can receive a badge with a unique, one-time use burner address that isn''t linked with anything else.

**Verifiable Credentials**

[Verifiable Credentials](https://www.w3.org/TR/vc-data-model-2.0/) are growing in popularity, especially in the blockchain space. They provide a mechanism to express [credentials](https://www.w3.org/TR/vc-data-model-2.0/#dfn-credential) on the Web in a way that is cryptographically secure, privacy respecting, and machine-verifiable.

While BitBadges and VCs have similar use cases, they can be used together in a complementary manner.&#x20;

* Create a badge and link it with a VC (either through the badge metadata or standards or custom data). To "have the credential", you must own the badge and prove the credential. This can be used in cases where the credential itself can be public and is to be displayed in a portfolio (BitBadges) but has certain aspects that may need to maintain prviate (VCs). This also enables VCs to be transferred and owned.&#x20;
  * For example, you may want a generic diploma badge in your BitBadges portfolio, but the GPA, date of graduation, etc should be private. With VCs, you can still attest to the private information to whoever needs it like employers.
* Use the BitBadges token standard to clearly define revocation / suspension rights. Maybe the entire credential should be private, but you can use an on-chain badge's status for the revocation part of a VC. For example, if the badge is burned, it is considered revoked.

We are working on formalizing the standards to use BitBadges and VCs together.

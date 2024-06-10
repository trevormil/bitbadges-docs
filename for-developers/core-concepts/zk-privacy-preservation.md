# 🕵️ ZK / Privacy Preservation

While all badge collections are public and created on the BitBadges blockchain, we recognize privacy is an important aspect in many cases. Here are some ways BitBadges can be used in a privacy-preserving context.

### **Verifiable Attestations**

Attestations are a centralized, off-chain feature that allow issuers to sign any message(s) cryptographically, store them via BitBadges, and generate proofs of their validity.

The most common use case is credentials where an issuer generates a credential, hands it out to an issuer, and the holder can generate proofs of validity to give to any potential verifier with zero trust involved. See the link below to learn more.

{% content-ref url="verifiable-attestations/" %}
[verifiable-attestations](verifiable-attestations/)
{% endcontent-ref %}

### **Proof of Knowledge for Approval**

Certain secrets may even be configured to perform on-chain actions (e.g. solve a zero-knowledge proof for approval or insert a secret code for approval). Some actions are natively supported (ZK Proofs, codes, etc), or you can self implement with CosmWasm.

### Privatizing Core Details

Some details are simply always public on the blockchain, but you can configure them in a way that does not leak information.

**Blank Metadata**

You could create your collection to be generic with a default placeholder metadata. Balances will still be public, but the collections' purpose will not be public.&#x20;

**One-Time Use Only Addresses**

You can also create / issue a collection with a unique one-time use only burner address. Or, in a similar manner, you can receive a badge with a unique, one-time use burner address that isn't linked with anything else.

**Gated / Hidden Balances**

You can also select the "Private" or "None" balances standard on the site (correlates to balance type = "Non-Public"). This will allow you to secretly define who owns the badge. Everything else will be public (metadata, etc), but ownership is dependent on you.

### **Zero Knowledge Proof of Ownership**

In some cases, you may want to prove you meet criteria without revealing your identity (e.g. own the over age 21 badge but don't reveal your name, etc)

Given a snapshot of a collection's balances or list of addresses that is agreed upon between a prover and a verifier, the prover can prove with a zero-knowledge proof that they own the private key to an address in the snapshot without revealing the private key.

**Examples**

For an implemented ZKP example of proving your address is in a tree / set of Ethereum addresses, check out the addr_membership.circom or pubkey_membership.circom at [https://github.com/personaelabs/spartan-ecdsa](https://github.com/personaelabs/spartan-ecdsa).

**Resources + Extensions**

You may want to extend this in different ways such as supporting Solana / other addresses, proving min balance owned, ownership times, and more. You can custom write your own circuit using Circom to do so.&#x20;

Note certain signature algorithms or functionality probably aren't implemented yet (BIP322), so your implementation would have to take that into account or custom write your own.

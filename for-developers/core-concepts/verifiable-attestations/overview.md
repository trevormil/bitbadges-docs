# Overview

An attestation represents claims about a subject, similar to a Verifiable Credential. The concept has gained significant traction in blockchain and Web3 spaces, where different implementations offer varying approaches to creating secure, verifiable, and privacy-respecting credentials.

They can take many forms:

* W3C Verifiable Credentials standard
* BitBadges attestations
* Credly credentials (Open Badges standard)
* Other attestation providers

Each approach offers different tradeoffs in terms of:

* Cryptographic security features
* Privacy mechanisms
* Machine verifiability
* Compatibility and standards compliance
* Implementation complexity

BitBadges aims to offer our own attestation service as well as integrate with tons of other providers as well to give you the most options possible. Alternative providers include:

* Credly Badges
* Custom Uploads (any plaintext or JSON)

Currently, our core suite supports two approaches. Both are cryptographically signed, and thus, data integrity is maintained. These are stored off-chain (centralized) but can be anchored or posted on-chain if desired.

* BBS+: Messages are signed with the BBS+ signature scheme and a proof of issuance is linked with another proof message signature. BBS+ signatures allow for zero-knowledge selective disclosure (reveal only M of N messages).&#x20;
  * The BBS+ signature algorithm allows an issuer to sign N messages to produce a signature. From that signature, a holder can derive a cryptographic proof that only reveals any subset of the N messages. This allows selectvie disclosure of the credential. For example, you may only want to reveal your GPA to an employer, but the diploma credential has other identifying fields like courses taken, student records, etc.
  * To create the link between a "main" crypto address and the BBS+ public key, we sign a message from the main address saying that attestations from BBS+ key can be treated as my own.
* Standard Signatures: Messages are signed via any supported wallet / ecosystem (Bitcoin, Ethereum, Solana, Cosmos). These do not support selective disclosure.

Implementation Note: While BitBadges provides a centralized storage solution for convenience, the attestation system's underlying architecture is decentralized. Users have the flexibility to:

* Use the provided storage solution
* Implement their own storage and verification systems
* Choose alternative attestation providers
* Mix different approaches based on specific needs

<figure><img src="../../../.gitbook/assets/image (134).png" alt="" width="375"><figcaption></figcaption></figure>

**Badges vs Attestations**

Badges and attestations can be used together to get the best of both worlds (e.g. issue a public diploma badge but issue private credentials about that diploma as a attestation).&#x20;

To "have the credential", you must prove ownership of the badge and the credential. This can be used in cases where the credential itself can be public and is to be displayed in a portfolio (BitBadges) but has certain aspects that may need to maintain private (attestations). The design also may enable credentials to be transferable.

Badges can also be used for on-chain, tamper-proof, decentralized revocation or suspension statuses. In the credential somewhere, you say that this badge must not be burned or revoken on-chain for it to be valid.

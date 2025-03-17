# Overview

An attestation represents claims about a subject. They can come in many different forms (signatures, BBS, simple URLs) as long as they are verifiable. Each scheme can offer varying tradeoffs. We support a couple natively while also allowing you to custom upload your attestation.

Currently, our core suite supports two approaches. Both are cryptographically signed, and thus, data integrity is maintained. These are stored off-chain (centralized) but can be anchored or posted on-chain if desired.

* BBS+: Messages are signed with the BBS+ signature scheme and a proof of issuance is linked with another proof message signature. BBS+ signatures allow for zero-knowledge selective disclosure (reveal only M of N messages).&#x20;
  * The BBS+ signature algorithm allows an issuer to sign N messages to produce a signature. From that signature, a holder can derive a cryptographic proof that only reveals any subset of the N messages. This allows selectvie disclosure of the credential. For example, you may only want to reveal your GPA to an employer, but the diploma credential has other identifying fields like courses taken, student records, etc.
  * To create the link between a "main" crypto address and the BBS+ public key, we sign a message from the main address saying that attestations from BBS+ key can be treated as my own.
* Standard Signatures: Messages are signed via any supported wallet / ecosystem (Bitcoin, Ethereum, Solana, Cosmos). These do not support selective disclosure.

To get started, simply create an attestation in-site.

<figure><img src="../../../.gitbook/assets/image (134).png" alt="" width="375"><figcaption></figcaption></figure>

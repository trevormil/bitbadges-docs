# 🔐 Verifiable Secrets

Verifiable secrets are very similar to Verifiable Credentials. [Verifiable Credentials](https://www.w3.org/TR/vc-data-model-2.0/) are growing in popularity, especially in the blockchain space. They provide a mechanism to express [credentials](https://www.w3.org/TR/vc-data-model-2.0/#dfn-credential) on the Web in a way that is cryptographically secure, privacy respecting, and machine-verifiable. While the BitBadges secrets implementation does not claim to be 100% compatible with the W3C Verifiable Credentials standard, it follows a very similar execution flow.&#x20;

Very simply, secrets are just cryptographic signatures of message(s). Because they are cryptographically signed by an issuer, it maintains the data integrity, so any holder / verifier of the secret can prove it was issued by the issuer (simply by checking the signature). All is facilitated off-chain, unless you want to anchor its existence (without revealing the data) on-chain for data commitment and time verification purposes.&#x20;

It is important to note that this is a centralized solution (i.e. we store the secrets) provided for convenience. Everything about secrets is already decentralized, and you can always choose to self-implement your own solution.&#x20;

**Badges and Secrets**

Badges and secrets are similar, and deciding which one to use is typically dependent on your use cases. They can even be used together to get the best of both worlds (e.g. issue a public diploma badge but issue private credentials about that diploma as a secret). To "have the credential", you must prove ownership of the badge and the credential. This can be used in cases where the credential itself can be public and is to be displayed in a portfolio (BitBadges) but has certain aspects that may need to maintain private (secrets). The design also may enable credentials to be transferable.

Badges can also be used for on-chain, tamper-proof, decentralized revocation or suspension statuses. In the credential somewhere, you say that this badge must not be burned or revoken on-chain for it to be valid.

**Differences in W3C Verifiable Credentials vs BitBadges**

With both BitBadges and W3C, issuers can sign arbitrary data. W3C expects everything to be in JSON-LD format with specific schemas (e.g. credentialSubject, context, etc) whereas BitBadges doesn't care what format it is in (we leave this up to the issuer). The data just has to be stringified. BitBadges also doesn't fully support the Verifiable Presentations interface because we opt to attach it to our sign-in protocol (Blockin) instead.&#x20;

Overall though, both achieve the same purpose, but the implementations differ slightly.

**Signature Algorithms**

BitBadges supports the signature algorithms for any chain that is supported.&#x20;

We also support the BBS+ signature algorithm to allow **selective disclosure**. The BBS+ signature algorithm allows an issuer to sign N messages to produce a signature. From that signature, a holder can derive a cryptographic proof that only reveals any subset of the N messages. This allows selectvie disclosure of the credential. For example, you may only want to reveal your GPA to an employer, but the diploma credential has other identifying fields like courses taken, student records, etc.

To create the link between a "main" crypto address and the BBS+ public key, we sign a message from the main address saying that secrets from BBS+ key can be treated as my own.

**References / Links**

API - Gets

Blockin tutorials




# Verifiable Attestations

**Off-Chain Attestations and On-Chain Anchoring**

Besides traditional on-chain badges, BitBadges supports the storage of off-chain verifiable attestations. These can be any form of data that is secured by a signature, such as credentials or attestations. The flexibility to store data off-chain while still having the option to anchor it on-chain for verification purposes is often useful for use cases that have privacy and security requirements. Attestations are just signatures of one or more message using a crypto wallet.

BitBadges offers a centralized, easy to use solution for attestations with many neat features. The centralized part just comes from the fact that BitBadges stores the signatures for you. If you would like to self-host, you can for a decentralized experience.

Some of the neat features include:

-   **Integration into Authentication Flows:** Attestations can be used to prove certain criteria to authentication providers. If you can prove you meet the criteria via a attestation, you will be authenticated. This can be integrated into the Sign In with BitBadges flow, so for example, you can prove public ownership of a diploma badge whilst also verifying attestation data such as a users' GPA.
-   **Anchoring:** Anchoring credentials on-chain provides a transparent and verifiable record of issuance, enhancing the trustworthiness and integrity of the credential without compromising the holder's privacy. The blockchain can be used for proof of knowledge of a attestation at a certain time or to maintain data integrity.

**Issuance and Storage**

The credential issuance process begins when an issuer (for example, a university) signs a piece of private data (such as a student’s diploma) and transfers this signed data, along with the credential, to a recipient (the student). This action gives the holder concrete proof of their credential, which they can then present to any verifier. The system also ensures that verifiers receive cryptographic assurance of the data's authenticity.

A distinctive feature of BitBadges is the ability for issuers to **selectively disclose** information. This means they can prove claims (like a GPA above 3.0) without disclosing any additional personal information that's irrelevant to the verifier's needs.

For data integrity and verification purposes, credentials are signed and stored off-chain in the holder's BitBadges account. Issuers have the option to anchor these credentials on-chain to commit to the data publicly and verify its issuance time, although this step is optional and not always necessary.

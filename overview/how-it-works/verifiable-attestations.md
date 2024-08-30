# Verifiable Attestations

### What Are Verifiable Attestations?

Verifiable Attestations are digital proofs similar to Verifiable Credentials, a concept gaining traction in the blockchain ecosystem. They enable individuals to prove claims about themselves (such as qualifications, achievements, or attributes) in a secure, tamper-evident, and privacy-preserving manner to any verifier.

#### What Is an Attestation?

An attestation, at its core, is a cryptographic signature of a message or set of claims. When an entity (known as the issuer) signs a message, it creates a cryptographic seal that ensures the integrity of the message's data. This process involves:

1. The issuer creating a message with specific claims or information
2. The issuer signing this message with their private key
3. The resulting signature being attached to the original message

Anyone who receives or checks the attestation can verify its authenticity and integrity by:

1. Checking the signature using the issuer's public key
2. Confirming that the message hasn't been altered since it was signed

Example: A university (issuer) creates and signs a digital diploma attestation for a graduate (holder). The graduate can then present this attestation to potential employers (verifiers) who can cryptographically verify its authenticity without needing to contact the university directly.

### Benefits of Using Verifiable Attestations

#### 1. Efficient Use of Blockchain Resources

Verifiable attestations are designed to be lightweight and efficient in terms of blockchain usage:

-   Most of the attestation process occurs off-chain, reducing the need for on-chain transactions
-   Only cryptographic proofs or minimal anchoring data may be stored on-chain, if necessary
-   This approach significantly reduces gas costs and blockchain bloat
-   Verification can often be done without any on-chain transactions, further saving resources

#### 2. Privacy by Default

Verifiable attestations prioritize user privacy:

-   The actual data contained in an attestation is not stored on the blockchain
-   Only the holder of the attestation has access to the full contents by default
-   Cryptographic proofs allow for verification without revealing the underlying data
-   Users have full control over when and how much of their attestation data to reveal
-   Optional on-chain anchoring can prove an attestation's existence without exposing its contents

#### 3. Zero-Knowledge Selective Disclosure

Verifiable attestations empower users with fine-grained control over their personal information:

-   Users can choose which specific parts of an attestation to share in different contexts
-   This allows for sharing only the necessary information for a given situation while not sacrificing the ability to verify the attestation cryptographically.
-   Example: A diploma attestation might include degree name, graduation date, and GPA. The holder could choose to share only the degree name and graduation date for one purpose, while sharing the full details for another.

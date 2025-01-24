# Attestations

Attestations are digital proofs that can attest to some credential or data. They enable individuals to prove claims about themselves (such as qualifications, achievements, or attributes) in a secure, tamper-evident, and privacy-preserving manner to any verifier. These are a nice alternative to badges because they are private by default as opposed to being public and on-chain.

BitBadges aims to support an ecosystem of providers and approaches to attestations. While we offer an attestation service natively, we also support custom uploads from thrird-party providers.&#x20;

Each method / approach may have different properties and tradeoffs. For example, BitBadges attestations are decentraized and secured by cryptography whereas other providers may use centralized verification methods.

Attestations are privately stored in your account by default but can be displayed on profiles and disclosed to verifiers as you need through flows like claims or Sign In with BitBadges.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-02 at 11.55.15 AM.png" alt="" width="563"><figcaption></figcaption></figure>

#### What Is an Attestation?

An attestation is a set of claims made by one entity (the issuer) about another entity or subject. Attestations can range from simple written statements to cryptographically verifiable digital proofs. When implemented digitally, attestations may include:

1. The issuer creating a message with specific claims or information
2. Optional digital signing or certification of the claims
3. A mechanism for verification (which may be manual or automated)

Verification methods can vary depending on the attestation type:

* For cryptographic attestations: Using digital signatures and public key verification
* For traditional attestations: Manual verification through trusted channels, official documentation, or direct communication
* For hybrid approaches: Combining digital and traditional verification methods

Example: A university (issuer) can create an attestation of a student's graduation in various forms:

* A traditional paper diploma with security features
* A digitally signed electronic certificate
* A blockchain-anchored credential
* A simple written statement

## Benefits of Attestations

1. Flexibility and Efficiency Attestations can be designed to match specific needs and resources:

* Can be implemented with or without blockchain technology
* May use traditional verification methods or cryptographic proofs
* Scale from simple statements to complex verifiable credentials
* Verification costs and complexity can be optimized for the use case

2. Privacy Considerations Modern attestation systems can protect privacy through various means:

* Selective information sharing
* Secure storage of sensitive data
* Optional use of cryptographic techniques for enhanced privacy
* Control over how and when attestation data is shared

3. Selective Disclosure Attestations can support varying levels of information sharing:

* Ability to share full or partial information as needed
* Support for both simple and complex verification requirements
* Flexible presentation options based on context
* Example: An academic credential might be shared in full for employment but only partially for other purposes

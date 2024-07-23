# Verifiable Attestations

### What Are Verifiable Attestations?

Verifiable Attestations are like Verifiable Credentials, which are becoming popular in the blockchain world. They let you prove something about yourself (like a credential) in a secure and privacy-respecting way to a verifier.

#### What Is an Attestation?

Simply put, an attestation is a cryptographic signature of a message. This means that when someone (an issuer) signs a message, it ensures the message's data stays intact. Anyone who receives or checks the attestation can verify that it was indeed issued by the issuer by checking the signature.

Example: A university (issuer) signs a diploma attestation for a student (holder)

### Benefits of Using Verifiable Attestations

#### 1. Uses Less Blockchain Resources

Since most of the process happens off-chain, verifiable attestations use fewer blockchain resources. This makes them efficient and cost-effective because you don't need to pay for blockchain transactions to create or verify an attestation.

#### 2. Private by Default

Verifiable attestations are private by default. The data in an attestation is not stored on the blockchain, which means it remains confidential. Only the cryptographic proof (the signature) is used for verification, ensuring your information stays private.

You may choose to reveal it publicly or anchor its existence on-chain, but by default, they remain private.

#### 3. Selective Disclosure

With verifiable attestations, you have the power of selective disclosure. This means you can choose what information to share and with whom. For example, you might show your diploma badge publicly but keep detailed information about your grades private. You control who sees the full details and who only sees the proof.

With BitBadges, we also support zero-knowledge selective disclosure through advanced cryptography. For example, lets say you have a diploma attestation which includes a few messages such as your GPA, attendance records, student records, etc. If an employer only needs to see your GPA, you can cryptographically reveal only the GPA even though the attestation has lots more details to it.

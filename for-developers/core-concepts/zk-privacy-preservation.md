# 🕵️ ZK / Privacy Preservation

While all badge collections on the BitBadges blockchain are public, the system provides several features and methods to preserve privacy in various contexts. This document outlines these privacy-preserving features and their applications.

By leveraging these privacy-preserving features, you can create systems that respect user privacy while still benefiting from the transparency and immutability of the BitBadges blockchain. Always consider the specific privacy requirements of your use case and implement the most appropriate combination of these features.

### Attestations

Attestations are an off-chain feature that allows issuers to cryptographically sign messages, store them via BitBadges or self-host, and generate proofs of their validity.

#### Key Points:

* Primarily used for credentials
* Issuer generates and distributes credentials
* Holders can generate proofs of validity for verifiers
* Zero trust involved in the verification process

### Proof of Knowledge for Approval

Certain secrets can be configured to perform on-chain actions:

* Solve a zero-knowledge proof for approval
* Insert a secret code for approval
* Natively supported actions: ZK Proofs, codes, etc.
* Custom implementations possible with CosmWasm

### Privatizing Core Details

While some details are always public on the blockchain, you can configure them to minimize information leakage.

#### Blank Metadata

* Create a collection with generic, placeholder metadata
* Balances remain public, but the collection's purpose is obscured

#### One-Time Use Only Addresses

* Create/issue collections using unique, one-time use burner addresses
* Receive badges with unique, one-time use burner addresses not linked to other activities

#### Gated / Hidden Balances

* Use "Private" or "None" balances standard (balance type = "Non-Public")
* Allows secret definition of badge ownership
* Public information: metadata, etc.
* Ownership information: dependent on the issuer

### Zero Knowledge Proof of Ownership

Prove criteria fulfillment without revealing identity:

1. Agree on a snapshot of collection balances or address list between prover and verifier
2. Prover demonstrates ownership of a private key for an address in the snapshot
3. Prover's identity remains concealed

#### Example Implementation

For a ZKP example proving address membership in a set of Ethereum addresses, see:

* `addr_membership.circom`
* `pubkey_membership.circom`

Available at: [https://github.com/personaelabs/spartan-ecdsa](https://github.com/personaelabs/spartan-ecdsa)


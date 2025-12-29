# ETH Signature Challenges

ETH Signature Challenges are a type of approval criteria that require users to provide valid Ethereum signatures from a predetermined signer to complete transfers. The signer approves the transfer by signing a message that contains the nonce and contextual information about the transfer. This feature allows for secure, on-chain verification of off-chain authorization without the complexity of Merkle trees.

## Overview

ETH Signature Challenges work by requiring users to provide Ethereum signatures that prove they have authorization from specific Ethereum addresses. Each signature can only be used once, preventing replay attacks and ensuring the security of the approval system.

## How It Works

### Signature Scheme

The signature scheme follows the pattern:

```
ETHSign(nonce + "-" + initiatorAddress + "-" + collectionId + "-" + approverAddress + "-" + approvalLevel + "-" + approvalId + "-" + challengeId)
```

Where:

-   `nonce`: A unique identifier provided by the user
-   `initiatorAddress`: The address initiating the transfer
-   `collectionId`: The ID of the collection being transferred
-   `approverAddress`: The address that needs to approve the transfer
-   `approvalLevel`: The approval level (e.g., "initial", "update")
-   `approvalId`: The ID of the approval being checked
-   `challengeId`: The challenge tracker ID
-   `-`: Literal dash characters separating the values

This comprehensive signature format ensures that signatures are bound to specific transfer contexts, preventing signature reuse across different transfers, collections, or approval scenarios.

### Challenge Structure

Each ETH Signature Challenge contains:

-   `signer`: The Ethereum address that must sign the challenge
-   `challengeTrackerId`: Unique identifier for tracking used signatures
-   `uri`: Optional metadata URI
-   `customData`: Optional custom data

### Proof Structure

Users provide ETH Signature Proofs containing:

-   `nonce`: The nonce that was signed (this is the only user-provided value in the signature message)
-   `signature`: The Ethereum signature of the full message string

## Key Features

### One-Time Use Signatures

Each signature can only be used once per challenge tracker. This prevents:

-   Replay attacks
-   Double-spending of approvals
-   Unauthorized reuse of signatures

The signature tracking is scoped to the combination of:

-   Collection ID
-   Approver address
-   Approval level
-   Approval ID
-   Challenge tracker ID
-   The signature itself

### Multiple Signers

You can require signatures from multiple Ethereum addresses in a single approval:

```json
{
    "ethSignatureChallenges": [
        {
            "signer": "0x1234567890123456789012345678901234567890",
            "challengeTrackerId": "challenge1"
        },
        {
            "signer": "0x0987654321098765432109876543210987654321",
            "challengeTrackerId": "challenge2"
        }
    ]
}
```

### Context-Aware Signatures

The new signature format includes contextual information about the transfer, ensuring that:

-   Signatures cannot be reused across different collections
-   Signatures cannot be reused across different approvers
-   Signatures cannot be reused across different approval levels
-   Signatures are bound to specific approval IDs and challenge IDs

This provides stronger security guarantees compared to simpler signature schemes.

## Implementation Details

### Signature Verification

The system verifies signatures by:

1. Constructing the signed message: `nonce + "-" + initiatorAddress + "-" + collectionId + "-" + approverAddress + "-" + approvalLevel + "-" + approvalId + "-" + challengeId`
2. Recovering the signer address from the signature using elliptic curve signature verification
3. Comparing the recovered address with the expected `signer` address
4. Checking that the signature hasn't been used before (tracked per collection, approver, approval level, approval ID, and challenge ID)

### Storage

Used signatures are tracked in the blockchain state using:

-   **Key**: `ETHSignatureTrackerKey` constructed from:
    -   Collection ID
    -   Approver address
    -   Approval level
    -   Approval ID
    -   Challenge tracker ID
    -   The signature itself
-   **Value**: Number of times the signature has been used (increment-only, must be 0 for first use)

### Proof Matching

The system checks all provided ETH signature proofs against each challenge. A challenge is satisfied if at least one proof:

1. Has a valid signature from the expected signer
2. Has not been used before
3. Matches the signature scheme with the correct contextual parameters

## Quick Reference

### Interface Definitions

```typescript
interface ETHSignatureChallenge {
    signer: string; // Ethereum address that must sign
    challengeTrackerId: string; // Unique ID for tracking used signatures
    uri?: string; // Optional metadata URI
    customData?: string; // Optional custom data
}

interface ETHSignatureProof {
    nonce: string; // The nonce that was signed (user-provided)
    signature: string; // Ethereum signature of the full message
}
```

### Signature Message Format

When signing, the message string is constructed as:

```
{nonce}-{initiatorAddress}-{collectionId}-{approverAddress}-{approvalLevel}-{approvalId}-{challengeId}
```

All values are joined with literal dash (`-`) characters. The signer must sign this exact string.

## Error Handling

Common error scenarios:

-   **Invalid Signature**: Signature doesn't match the expected signer or the message format is incorrect
-   **Already Used**: Signature has been used before for this specific combination of collection, approver, approval level, approval ID, and challenge ID
-   **Missing Proof**: Required ETH signature proof not provided
-   **Invalid Nonce**: Nonce format or content is invalid
-   **Context Mismatch**: Signature was created for a different transfer context (different collection, approver, etc.)

The system provides clear error messages to help users understand and resolve issues.

## Security Considerations

### Replay Attack Prevention

The combination of contextual information in the signature and one-time-use tracking prevents:

-   Reusing signatures across different transfers
-   Reusing signatures across different collections
-   Reusing signatures across different approvers
-   Reusing signatures after the challenge tracker ID changes

### Signature Binding

By including transfer context in the signature, the system ensures that:

-   Signatures cannot be extracted and used in unauthorized contexts
-   Each signature is cryptographically bound to specific transfer parameters
-   Signers can review exactly what they are approving before signing

### Challenge Tracker ID Management

Changing the `challengeTrackerId` resets the usage tracker, allowing signatures to be reused. This can be useful for:

-   Rotating challenge configurations
-   Resetting usage counters
-   Creating new challenge periods

However, care should be taken to ensure old signatures cannot be maliciously reused when tracker IDs are changed.

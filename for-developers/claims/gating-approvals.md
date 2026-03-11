# Gating On-Chain Approvals

Claims can gate on-chain token operations (mints, transfers). This is a hybrid off-chain/on-chain process where claims control the **right to initiate** a transfer, not the transfer itself.

## How It Works

```
1. User completes claim  →  receives Merkle proof leaf (one-time use code - handled behind the scenes)
2. User submits proof on-chain  →  in a transfer/mint transaction
3. On-chain approval criteria  →  validates the Merkle proof
```

The claim issues a Merkle proof leaf — a one-time use code signed by BitBadges for the approved address. The user then includes this proof in their on-chain `MsgTransferTokens`. The on-chain Merkle challenge verifies the proof is valid and hasn't been used before and was signed by BitBadges intended for the approved address.

## On-Chain Types

### Merkle Challenge (Approval Criteria)

Configured on the collection's approval criteria:

```typescript
interface iMerkleChallenge<T extends NumberType> {
    root: string; // Merkle tree root hash
    maxUsesPerLeaf: T; // Max uses per leaf (1 = one-time use)
    uri: string; // Where to fetch merkle metadata
    customData: string; // Arbitrary on-chain data
    challengeTrackerId: string; // Tracker ID for usage tracking
    leafSigner: string; // Address that signs leaves (ETH address)
}
```

`merkleChallenges` is a field on `iOutgoingApprovalCriteria`, `iIncomingApprovalCriteria`, and collection-level approval criteria. It can be used on any level - collection, sender, or recipient.

### Merkle Proof (User Submission)

Passed in `MsgTransferTokens`:

```typescript
interface iMerkleProof {
    aunts: iMerklePathItem[]; // Path from leaf to root
    leaf: string; // The leaf value (one-time use code)
    leafSignature: string; // ETH signature: sign(leaf + "-" + intendedBitBadgesAddress)
}

interface iMerklePathItem {
    aunt: string; // Sibling hash at this level
    onRight: boolean; // Whether the sibling is on the right
}
```

### Challenge Tracker

The chain tracks which leaves have been used:

```typescript
interface iMerkleChallengeTrackerDoc<T extends NumberType> {
    collectionId: CollectionId;
    challengeTrackerId: string;
    approvalId: string;
    approvalLevel: 'collection' | 'incoming' | 'outgoing' | '';
    approverAddress: BitBadgesAddress;
    usedLeafIndices: iUsedLeafStatus<T>[]; // Which leaves are consumed
}
```

## The Hybrid Process

### Step 1: Off-Chain Claim

The user completes the claim on BitBadges. All configured plugins are evaluated (codes, passwords, whitelists, custom logic, etc.). If successful, BitBadges assigns the user a Merkle proof leaf from the pre-built Merkle tree.

### Step 2: On-Chain Transaction

The user submits a `MsgTransferTokens` with the Merkle proof. The chain:

1. Verifies the leaf signature matches the sender's address
2. Verifies the Merkle path from leaf to root
3. Checks the root matches the approval criteria's `merkleChallenges`
4. Marks the leaf as used in the challenge tracker (preventing replay)
5. Executes the transfer if all other approval criteria also pass

### What the User Sees

On the BitBadges site, this is seamless — the user completes the claim, then signs and submits the transaction. The Merkle proof is handled behind the scenes. Via API, you receive the proof from `completeClaim()` and include it in the transaction.

## Linking Claims to On-Chain Approvals

When a claim gates an on-chain approval, the claim document stores the link via `trackerDetails`:

```typescript
interface iChallengeTrackerIdDetails<T extends NumberType> {
  collectionId: CollectionId;          // The collection this claim is linked to
  approvalId: string;                  // The specific approval ID
  challengeTrackerId: string;          // The Merkle challenge tracker ID
  approvalLevel: 'collection' | 'incoming' | 'outgoing' | '';  // Which approval level
  approverAddress: BitBadgesAddress;   // The approver (manager, sender, or recipient)
}
```

Other relevant fields on the claim document:

| Field | Purpose |
|-------|---------|
| `collectionId` | Positive integer = on-chain claim linked to that collection. Off-chain claims use a non-positive value. |
| `docClaimed` | Must be `true` for the claim to be active and distributable. Set when the claim is finalized and linked to the collection on-chain. |
| `cid` | For on-chain claims, this matches the `challengeTrackerId` and is used to match claims to their on-chain Merkle challenge trackers. |
| `action.seedCode` | The encrypted seed used to generate one-time codes / Merkle leaves. Only decrypted for authorized managers. |

These fields are managed automatically when you create claims through the claim builder UI. If building claims programmatically via the API, ensure `trackerDetails` matches the on-chain approval's Merkle challenge configuration.

## Consistency Requirements

Because this is a hybrid system, **off-chain criteria and on-chain approval criteria must be aligned for proper design**. The off-chain claim checks your plugins and issues Merkle proofs; the on-chain approval validates the proof and executes the transfer. Misalignment causes failures. For example, if you allow 100 codes, but the Merkle tree has 50 leaves, the last 50 users will claim successfully off-chain but fail on-chain.

### Common Misalignment Scenarios

| Scenario                                                       | Result                                                                        |
| -------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Claim allows 100 codes, but Merkle tree has 50 leaves          | Last 50 users claim successfully off-chain but fail on-chain                  |
| On-chain approval updated without updating the claim           | Users receive proofs for a stale Merkle root                                  |
| Claim time window differs from on-chain `transferTimes`        | Users claim outside the on-chain window, proof is valid but transfer rejected |
| On-chain `initiatedBy` doesn't match claim's allowed addresses | Proof is valid but sender is rejected by the approval                         |

### Best Practices

- **Use the claim builder.** The in-site claim builder generates the on-chain Merkle tree and off-chain claim together, ensuring alignment.
- **Update both sides together.** If you change on-chain approval criteria, update the corresponding claim. If you update the claim, regenerate the Merkle tree.
- **Match limits.** `numUses` on the claim should equal the number of Merkle leaves and overall maximum number of transfers. `transferTimes` on the claim should fall within the on-chain approval's time window.

For trust assumptions, security risks, and mitigation strategies, see [Security & Trust Assumptions](security.md). It is recommended to NOT use this approach for high-stakes use cases.

## Permissions & Manager Authority

On-chain claims are linked to approvals at different levels, and the permission to manage them depends on who owns the approval:

- **Collection-level approvals:** Only the collection **manager** can create and manage claims linked to collection approvals. BitBadges verifies the claim creator is the current manager.
- **Sender (outgoing) approvals:** Only the **sender address** can manage claims linked to their outgoing approvals.
- **Recipient (incoming) approvals:** Only the **recipient address** can manage claims linked to their incoming approvals.

### Manager Transfers

The most important implication is for collection-level approvals: **if the manager role is transferred to a new address, claim management permissions transfer with it.** The new manager gains full control over all claims linked to the collection's approvals, and the previous manager loses access.

This means:
- Transferring the manager role is effectively transferring control of the collection's claim infrastructure
- The new manager can update, reconfigure, or disable existing claims
- If you use a manager splitter or multisig, all signers share claim management authority

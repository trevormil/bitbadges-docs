# Approval Criteria

Additional restrictions and conditions that determine whether a transfer is approved beyond the basic approval matching.

## Interface

```typescript
export interface iApprovalCriteria<T extends NumberType> {
    /** The BADGE or other sdk.Coin transfers to be executed upon every approval. */
    coinTransfers?: iCoinTransfer<T>[];
    /** The list of merkle challenges that need valid proofs to be approved. */
    merkleChallenges?: iMerkleChallenge<T>[];
    /** The list of must own tokens that need valid proofs to be approved. */
    mustOwnTokens?: iMustOwnToken<T>[];
    /** The predetermined balances for each transfer. These allow approvals to use predetermined balance amounts rather than an incrementing tally system. */
    predeterminedBalances?: iPredeterminedBalances<T>;
    /** The maximum approved amounts for this approval. */
    approvalAmounts?: iApprovalAmounts<T>;
    /** The max num transfers for this approval. */
    maxNumTransfers?: iMaxNumTransfers<T>;
    /** Whether the approval should be deleted after one use. */
    autoDeletionOptions?: iAutoDeletionOptions;
    /** Whether the to address must equal the initiatedBy address. */
    requireToEqualsInitiatedBy?: boolean;
    /** Whether the from address must equal the initiatedBy address. */
    requireFromEqualsInitiatedBy?: boolean;
    /** Whether the to address must not equal the initiatedBy address. */
    requireToDoesNotEqualInitiatedBy?: boolean;
    /** Whether the from address must not equal the initiatedBy address. */
    requireFromDoesNotEqualInitiatedBy?: boolean;
    /** Whether this approval overrides the from address's approved outgoing transfers. */
    overridesFromOutgoingApprovals?: boolean;
    /** Whether this approval overrides the to address's approved incoming transfers. */
    overridesToIncomingApprovals?: boolean;
    /** The royalties to apply to the transfer. */
    userRoyalties?: iUserRoyalties<T>;
    /** The list of dynamic store challenges that must pass for approval. Can check initiator, sender, recipient, or a hardcoded address. */
    dynamicStoreChallenges?: iDynamicStoreChallenge<T>[];
    /** The list of ETH signature challenges that require valid Ethereum signatures for approval. */
    ethSignatureChallenges?: iETHSignatureChallenge<T>[];
    /** The list of voting challenges that require weighted quorum thresholds to be met. */
    votingChallenges?: iVotingChallenge<T>[];
    /** Address checks for sender */
    senderChecks?: iAddressChecks;
    /** Address checks for recipient */
    recipientChecks?: iAddressChecks;
    /** Address checks for initiator */
    initiatorChecks?: iAddressChecks;
    /** Alternative time-based checks for approval denial (offline hours/days). */
    altTimeChecks?: iAltTimeChecks;
    /** Whether this approval must be prioritized during evaluation. */
    mustPrioritize?: boolean;
    /** Whether this approval can be used for IBC backed path operations (collection-level only). */
    allowBackedMinting?: boolean;
    /** Whether this approval can be used for cosmos coin wrapper path operations (collection-level only). */
    allowSpecialWrapping?: boolean;
    /** The list of EVM query challenges that must pass for approval. Executes read-only calls to EVM contracts. */
    evmQueryChallenges?: iEVMQueryChallenge[];
}
```

## Core Components

* [**Approval Trackers**](approval-trackers.md) - Tracking transfer amounts and counts
* [**Tallied Approval Amounts**](tallied-approval-amounts.md) - Amount limits and thresholds
* [**Max Number of Transfers**](max-number-of-transfers.md) - Transfer count limits
* [**Predetermined Balances**](predetermined-balances.md) - Exact balance requirements
* [**Merkle Challenges**](merkle-challenges.md) - Cryptographic proof requirements
* [**Dynamic Store Challenges**](dynamic-store-challenges.md) - On-chain numeric checks
* [**ETH Signature Challenges**](eth-signature-challenges.md) - Ethereum signature requirements
* [**Voting Challenges**](voting-challenges.md) - Weighted quorum threshold requirements
* [**Token Ownership**](badge-ownership.md) - Required token holdings
* [**Cosmos Coin Transfers**](usdbadge-transfers.md) - Payments per approval (BADGE or other sdk.Coin)
* [**Overrides**](overrides.md) - Bypassing user-level approvals
* [**Requires**](requires.md) - Address relationship restrictions
* [**Address Checks**](address-checks.md) - Address type restrictions (EVM contracts, liquidity pools)
* [**Auto-Deletion Options**](auto-deletion-options.md) - Automatic approval cleanup
* [**User Royalties**](user-royalties.md) - Percentage-based transfer fees
* [**Alt Time Checks**](../../../x-tokenization/concepts/approval-criteria/alt-time-checks.md) - Time-based restrictions (offline hours/days)
* [**Must Prioritize**](../../../x-tokenization/concepts/approval-criteria/must-prioritize.md) - Requiring explicit approval prioritization
* [**Special Address Flags**](special-address-flags.md) - Control approval eligibility for backed minting and special wrapping (collection-level only)
* [**EVM Query Challenges**](evm-query-challenges.md) - Read-only EVM contract queries as approval conditions

## Key Concepts

### Tracker IDs

Trackers use IDs with format: `approvalId-trackerId` plus identifying details. All trackers are scoped to a specific `approvalId`.

**Important**: Trackers are increment-only and immutable. Never reuse tracker IDs with prior history.

### Best Practices - Creating / Updating / Deleting

Trackers are increment-only and immutable. Never reuse tracker IDs with prior history when creating approvals that should start from scratch.

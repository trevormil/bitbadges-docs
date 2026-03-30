# Intent / OTC Offer

This guide explains how to create an over-the-counter (OTC) swap offer using BitBadges collection primitives. No chain changes are required -- this pattern composes existing features: token IDs for partitioning, coin transfers for payment/payout, and the mint escrow for escrowing the offered asset.

## Overview

An **intent** is a collection that represents an open offer to swap one asset for another. For example, "I am selling 1 ATOM for 100 USDC." Anyone can fill the offer by transferring a token from the collection, which atomically triggers the coin swap.

Key properties:

* **Atomic** -- payment and payout happen in the same transaction as the token transfer.
* **Partially fillable** -- the offer is split into N equal partitions (token IDs), so fillers can take any portion.
* **Trustless** -- the offered asset is escrowed in the mint escrow address at creation time. No counterparty risk.
* **Expirable** -- time-based transfer windows let offers auto-expire.

## How It Works

1. The creator decides on the offered asset, requested asset, and number of partitions.
2. A collection is created with N token IDs (one per partition).
3. A single collection-level approval is configured with **two coin transfers**:
   * **Payment** -- the filler pays X of the requested asset per partition.
   * **Payout** -- the filler receives Y of the offered asset per partition, funded from the mint escrow via `overrideFromWithApproverAddress: true`.
4. The creator escrows the total offered asset into the mint escrow address at creation.
5. To fill, a user initiates a transfer of one or more token IDs from Mint. The approval triggers both coin transfers atomically.
6. The collection is tagged with the `intent` standard for discoverability.

## Step-by-Step Setup

We will walk through a concrete example: **selling 1 ATOM (1,000,000 uatom) for 100 USDC (100,000,000 uusdc) in 10 partitions**.

Each fill costs 10 USDC and pays out 0.1 ATOM.

### 1. Choose Your Parameters

| Parameter | Value |
| --- | --- |
| Offered asset | `uatom` |
| Offered amount (total) | `1000000` (1 ATOM) |
| Requested asset | `uusdc` |
| Requested amount (total) | `100000000` (100 USDC) |
| Number of partitions | `10` |
| Per-partition payout | `100000` uatom (0.1 ATOM) |
| Per-partition cost | `10000000` uusdc (10 USDC) |

### 2. Define Valid Token IDs

Each partition is one token ID. For 10 partitions, use token IDs 1 through 10.

```typescript
const validTokenIds = [{ start: '1', end: '10' }];
```

### 3. Configure the Approval with Coin Transfers

The core of the intent is a single collection-level approval from Mint that includes two coin transfers.

```typescript
const intentApproval = {
  fromListId: 'Mint',
  toListId: 'All',
  initiatedByListId: 'All',
  transferTimes: [{ start: '1', end: '18446744073709551615' }],
  ownershipTimes: [{ start: '1', end: '18446744073709551615' }],
  tokenIds: [{ start: '1', end: '10' }],
  approvalId: 'intent-fill',
  version: '0',
  approvalCriteria: {
    coinTransfers: [
      {
        // Payment: filler pays 10 USDC to the creator
        to: 'bb1_CREATOR_ADDRESS',
        coins: [{ denom: 'uusdc', amount: '10000000' }],
        overrideFromWithApproverAddress: false, // from = initiator (filler)
        overrideToWithInitiator: false,
      },
      {
        // Payout: filler receives 0.1 ATOM from the mint escrow
        to: '', // overridden to initiator below
        coins: [{ denom: 'uatom', amount: '100000' }],
        overrideFromWithApproverAddress: true, // from = mint escrow address
        overrideToWithInitiator: true, // to = initiator (filler)
      },
    ],

    // Each filler can only fill one partition per transfer
    maxNumTransfers: {
      overallMaxNumTransfers: '0', // 0 = unlimited
      perToAddressMaxNumTransfers: '0',
      perFromAddressMaxNumTransfers: '0',
      perInitiatedByAddressMaxNumTransfers: '0',
      amountTrackerId: 'intent-fill-tracker',
      resetTimeIntervals: { startTime: '0', intervalLength: '0' },
    },

    // Override Mint outgoing approvals (required for Mint transfers)
    overridesFromOutgoingApprovals: true,
    overridesToIncomingApprovals: true,

    // Everything else: defaults
    merkleChallenges: [],
    ethSignatureChallenges: [],
    predeterminedBalances: {
      manualBalances: [],
      incrementedBalances: {
        startBalances: [],
        incrementTokenIdsBy: '0',
        incrementOwnershipTimesBy: '0',
        durationFromTimestamp: '0',
        allowOverrideTimestamp: false,
        recurringOwnershipTimes: { startTime: '0', intervalLength: '0' },
        allowOverrideWithAnyValidToken: false,
      },
      orderCalculationMethod: {
        useOverallNumTransfers: false,
        usePerToAddressNumTransfers: false,
        usePerFromAddressNumTransfers: false,
        usePerInitiatedByAddressNumTransfers: false,
        useMerkleChallengeLeafIndex: false,
        challengeTrackerId: '',
      },
    },
    approvalAmounts: {
      overallApprovalAmount: '0',
      perToAddressApprovalAmount: '0',
      perFromAddressApprovalAmount: '0',
      perInitiatedByAddressApprovalAmount: '0',
      amountTrackerId: 'intent-fill-amounts',
      resetTimeIntervals: { startTime: '0', intervalLength: '0' },
    },
    requireToEqualsInitiatedBy: false,
    requireFromEqualsInitiatedBy: false,
    requireToDoesNotEqualInitiatedBy: false,
    requireFromDoesNotEqualInitiatedBy: false,
    autoDeletionOptions: { afterOneUse: false, afterOverallMaxNumTransfers: false },
    userRoyalties: { percentage: '0', payoutAddress: '' },
    mustOwnTokens: [],
    dynamicStoreChallenges: [],
    votingChallenges: [],
    evmQueryChallenges: [],
    senderChecks: {
      mustBeEvmContract: false,
      mustNotBeEvmContract: false,
      mustBeLiquidityPool: false,
      mustNotBeLiquidityPool: false,
    },
    recipientChecks: {
      mustBeEvmContract: false,
      mustNotBeEvmContract: false,
      mustBeLiquidityPool: false,
      mustNotBeLiquidityPool: false,
    },
    initiatorChecks: {
      mustBeEvmContract: false,
      mustNotBeEvmContract: false,
      mustBeLiquidityPool: false,
      mustNotBeLiquidityPool: false,
    },
    altTimeChecks: { offlineHours: [], offlineDays: [] },
    mustPrioritize: false,
  },
};
```

The two `coinTransfers` entries are the key:

* The first sends the requested asset (USDC) **from the filler to the creator**.
* The second sends the offered asset (ATOM) **from the mint escrow to the filler**, using `overrideFromWithApproverAddress: true` and `overrideToWithInitiator: true`.

### 4. Escrow the Offered Asset

When creating the collection, use `mintEscrowCoinsToTransfer` to deposit the total offered amount into the mint escrow in a single transaction.

```typescript
const msgCreateCollection = {
  creator: 'bb1_CREATOR_ADDRESS',
  collectionId: '0', // auto-assigned
  validTokenIds: [{ start: '1', end: '10' }],
  collectionApprovals: [intentApproval],
  standards: ['intent'],
  mintEscrowCoinsToTransfer: [
    { denom: 'uatom', amount: '1000000' }, // 1 ATOM total escrow
  ],
  // ... other fields (metadata, permissions, defaultBalances, etc.)
};
```

### 5. Tag with the Intent Standard

Set the `standards` field to include `intent` so indexers and UIs can discover the collection as a fillable offer.

```typescript
standards: ['intent'],
```

## Filling an Intent

To fill one partition, a user sends a `MsgTransferTokens` that transfers a single token ID from Mint to themselves. The approval matches and triggers both coin transfers automatically.

```typescript
const fillMsg = {
  creator: 'bb1_FILLER_ADDRESS',
  collectionId: '<COLLECTION_ID>',
  transfers: [
    {
      from: 'Mint',
      toAddresses: ['bb1_FILLER_ADDRESS'],
      balances: [
        {
          amount: '1',
          ownershipTimes: [{ start: '1', end: '18446744073709551615' }],
          tokenIds: [{ start: '3', end: '3' }], // pick any unfilled token ID
        },
      ],
    },
  ],
};
```

When this executes:

1. Token ID 3 is minted to the filler.
2. 10 USDC is transferred from the filler to the creator.
3. 0.1 ATOM is transferred from the mint escrow to the filler.

To fill multiple partitions at once, include multiple token IDs in the transfer. Each token ID triggers the coin transfer pair once.

## Cancellation

The creator can cancel unfilled partitions by updating the collection:

1. **Remove the approval** -- use `MsgSetCollectionApprovals` to remove or replace the intent approval, preventing further fills.
2. **Withdraw escrowed funds** -- add a new approval that transfers remaining escrowed coins back to the creator (using `overrideFromWithApproverAddress: true` and a specific `to` address pointing to the creator).

Alternatively, if the collection permissions allow it, the creator can delete the collection entirely with `MsgDeleteCollection`.

## Expiry

Use the `transferTimes` field on the approval to set an expiry window. After the window closes, no more fills can occur.

```typescript
// Offer expires at Unix timestamp 1750000000 (approx. June 2025)
transferTimes: [{ start: '1', end: '1750000000' }],
```

After expiry, the creator can withdraw remaining escrowed funds using the cancellation flow described above.

## Summary

| Concept | Implementation |
| --- | --- |
| Offer partitioning | N token IDs (one per chunk) |
| Payment (filler to creator) | Coin transfer with default from/to |
| Payout (escrow to filler) | Coin transfer with `overrideFromWithApproverAddress` + `overrideToWithInitiator` |
| Escrow | `mintEscrowCoinsToTransfer` at creation |
| Discoverability | `intent` standard tag |
| Partial fills | Transfer any subset of remaining token IDs |
| Expiry | `transferTimes` window on the approval |
| Cancellation | Remove approval + withdraw escrow |

## Related

* [Coin Transfers](../../token-standard/learn/approval-criteria/usdbadge-transfers.md) -- coin transfer field reference
* [Building Collection Approvals](building-collection-approvals.md) -- approval patterns
* [Mint All to Self Tutorial](mint-all-to-self-tutorial.md) -- collection creation basics

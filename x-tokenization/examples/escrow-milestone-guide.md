# 🏦 How to Build a USDC-Backed Milestone Escrow Without a Smart Contract

Freelance payment disputes cost billions every year. Traditional escrow services charge 3-6% fees, and writing a custom smart contract means paying for an audit. BitBadges lets you build a fully on-chain milestone escrow using the token standard alone -- no Solidity, no intermediary, no audit bill.

This guide walks through creating a USDC-backed escrow vault that releases funds in three milestones (30% / 45% / 25%) as a freelance project progresses, with a 90-day timeout refund and a protocol-enforced guarantee that earned funds can never be clawed back.

## Architecture Overview

The escrow is a BitBadges collection backed 1:1 by USDC via IBC. Here is how each piece maps to a standard feature:

| Requirement | BitBadges Primitive |
| --- | --- |
| Lock real USDC in the vault | [IBC-backed minting](../../learn/ibc-backed-minting.md) via [Cosmos Coin Wrapper Paths](../../learn/cosmos-coin-wrapper-paths.md) |
| Only release funds when milestones are confirmed | Manager-controlled [collection approvals](../../learn/transferability.md) |
| Refund remaining funds after 90 days | Time-scoped approval with [auto-deletion](../../token-standard/learn/approval-criteria/auto-deletion-options.md) |
| Guarantee earned funds cannot be reversed | `noForcefulPostMintTransfers` invariant, locked via [permissions](../../learn/permissions.md) |
| Prevent free peer-to-peer transfers | Restricted [transferability](../../learn/transferability.md) |

**Roles:**

* **Client** -- deposits USDC at project start.
* **Provider** (freelancer) -- withdraws earned USDC as milestones are completed.
* **Manager** (arbiter) -- confirms milestone completion by updating approvals. Can be the client, a mutual third party, or a DAO.

## Step 1: Create the Collection with USDC Wrapping

The collection is created with `MsgUniversalUpdateCollection`. The key fields are the invariants that lock down the vault rules at the protocol level.

```json
{
  "invariants": {
    "noCustomOwnershipTimes": false,
    "maxSupplyPerId": "10000000000",
    "noForcefulPostMintTransfers": true,
    "disablePoolCreation": true,
    "cosmosCoinBackedPath": {
      "conversion": {
        "sideA": {
          "amount": "1",
          "denom": "ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349"
        },
        "sideB": [
          {
            "amount": "1",
            "tokenIds": [{ "start": "1", "end": "1" }],
            "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }]
          }
        ]
      }
    }
  }
}
```

What each invariant does:

* **`noForcefulPostMintTransfers: true`** -- Once the provider withdraws earned USDC, nobody (not even the manager) can reverse it. This is the core safety guarantee.
* **`disablePoolCreation: true`** -- Prevents the escrow tokens from being added to a liquidity pool.
* **`cosmosCoinBackedPath`** -- Establishes the 1:1 USDC backing. The IBC denom shown here is the USDC denom on BitBadges. Every escrow token minted has exactly one USDC locked behind it.
* **`maxSupplyPerId`** -- Caps the total escrow at 10,000 USDC (adjust per project).

## Step 2: Lock the Permissions

The collection permissions ensure the manager can update approvals (to release milestones) but cannot change the manager address, delete the collection, or alter the standards. This prevents the arbiter from hijacking the escrow.

```json
{
  "collectionPermissions": {
    "canDeleteCollection": [
      {
        "permanentlyPermittedTimes": [],
        "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }]
      }
    ],
    "canUpdateManager": [
      {
        "permanentlyPermittedTimes": [],
        "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }]
      }
    ],
    "canUpdateStandards": [
      {
        "permanentlyPermittedTimes": [],
        "permanentlyForbiddenTimes": [{ "start": "1", "end": "18446744073709551615" }]
      }
    ],
    "canUpdateCollectionApprovals": [
      {
        "fromListId": "All",
        "toListId": "All",
        "initiatedByListId": "All",
        "transferTimes": [{ "start": "1", "end": "18446744073709551615" }],
        "tokenIds": [{ "start": "1", "end": "1" }],
        "ownershipTimes": [{ "start": "1", "end": "18446744073709551615" }],
        "approvalId": "All",
        "permanentlyPermittedTimes": [{ "start": "1", "end": "18446744073709551615" }],
        "permanentlyForbiddenTimes": []
      }
    ]
  }
}
```

Key takeaways:

* **Deletion, manager changes, and standards** are permanently forbidden -- nobody can rug-pull the escrow.
* **Collection approvals are permanently permitted** -- the manager can always add new milestone release approvals. This is how milestones get unlocked.

For a full reference on how permissions work, see [Manager / Permissions](../../learn/permissions.md).

## Step 3: Configure Milestone Tranches

At creation time, the collection starts with only the backing and unbacking approvals (for depositing and withdrawing USDC) and a restricted transfer rule that blocks free peer-to-peer transfers.

```json
{
  "collectionApprovals": [
    {
      "approvalId": "smart-token-backing",
      "fromListId": "BACKING_ADDRESS",
      "toListId": "!BACKING_ADDRESS",
      "initiatedByListId": "All",
      "approvalCriteria": {
        "mustPrioritize": true,
        "allowBackedMinting": true
      }
    },
    {
      "approvalId": "smart-token-unbacking",
      "fromListId": "!Mint:BACKING_ADDRESS",
      "toListId": "BACKING_ADDRESS",
      "initiatedByListId": "All",
      "approvalCriteria": {
        "mustPrioritize": true,
        "allowBackedMinting": true
      }
    },
    {
      "approvalId": "restricted-transfer",
      "fromListId": "!Mint",
      "toListId": "All",
      "initiatedByListId": "All",
      "approvalCriteria": {}
    }
  ]
}
```

As each milestone is completed, the manager sends a new `MsgUniversalUpdateCollection` (or the granular `MsgSetCollectionApprovals`) to add a withdrawal approval scoped to the provider address and amount.

**Example: releasing Milestone 1 (30% of 10,000 USDC = 3,000 USDC)**

The manager adds a new approval that allows the provider to withdraw 3,000 tokens through the unbacking path:

```json
{
  "approvalId": "milestone-1-release",
  "fromListId": "!Mint:BACKING_ADDRESS",
  "toListId": "PROVIDER_ADDRESS",
  "initiatedByListId": "PROVIDER_ADDRESS",
  "approvalCriteria": {
    "mustPrioritize": true,
    "approvalAmounts": {
      "overallApprovalAmount": "3000000000"
    }
  }
}
```

The same pattern repeats for Milestone 2 (4,500 USDC) and Milestone 3 (2,500 USDC).

## Step 4: Set the 90-Day Timeout Refund

To protect the client if the project stalls, configure a refund approval with a transfer time window that only opens after 90 days. You can set this up at collection creation so the timeout is trustlessly enforced.

```json
{
  "approvalId": "timeout-refund",
  "fromListId": "!Mint:BACKING_ADDRESS",
  "toListId": "CLIENT_ADDRESS",
  "initiatedByListId": "CLIENT_ADDRESS",
  "transferTimes": [
    {
      "start": "UNIX_TIMESTAMP_90_DAYS_FROM_NOW",
      "end": "18446744073709551615"
    }
  ],
  "approvalCriteria": {
    "mustPrioritize": true,
    "approvalAmounts": {
      "overallApprovalAmount": "10000000000"
    }
  }
}
```

After the 90-day mark, the client can withdraw any remaining escrowed funds without needing the manager to take action.

For more on time-scoped approvals, see [Auto-Deletion Options](../../token-standard/learn/approval-criteria/auto-deletion-options.md).

## Step 5: Verify with the Audit Tool

Before deploying, run the audit tool to confirm the configuration matches your intent.

```bash
# Using the MCP builder
bitbadges audit_collection --collectionId YOUR_COLLECTION_ID
```

The audit checks:

* USDC backing is correctly configured
* The no-clawback invariant is active
* Transfer restrictions are in place
* Permissions are locked as expected
* Milestone approval amounts sum to the total escrow amount

You can also use the BitBadges site to visually inspect the collection rules after creation.

## How It Works Under the Hood

1. **Client deposits USDC.** The client sends USDC via IBC to the backing address. The chain mints wrapped escrow tokens 1:1 into the vault. See [Cosmos Coin Wrapper Paths](../../learn/cosmos-coin-wrapper-paths.md) for how the wrapping mechanism works.

2. **Manager confirms a milestone.** The manager sends a transaction updating the collection approvals to add a withdrawal approval scoped to the provider's address and the milestone amount. See [Transferability / Approvals](../../learn/transferability.md) for how approvals control token movement.

3. **Provider withdraws earned funds.** The provider burns their escrow tokens through the unbacking path, receiving the equivalent USDC. Once withdrawn, the `noForcefulPostMintTransfers` invariant guarantees these funds can never be reversed.

4. **Timeout triggers (if needed).** If milestones remain incomplete after 90 days, the time-scoped refund approval activates, allowing the client to withdraw remaining funds. See [Auto-Deletion Options](../../token-standard/learn/approval-criteria/auto-deletion-options.md) for how time-based approval windows work.

## Why Not a Smart Contract?

| | Smart Contract Escrow | BitBadges Token Escrow |
| --- | --- | --- |
| **Audit cost** | $5,000 - $50,000+ | $0 (uses audited chain primitives) |
| **Platform fee** | Varies (0-5%) | Gas only |
| **Clawback risk** | Depends on contract logic | Protocol-enforced invariant |
| **Setup time** | Days to weeks | Minutes |
| **Transparency** | Must read Solidity | Rules readable on-chain and in the UI |

## Next Steps

* Try the [AI builder](https://bitbadges.io) -- describe your escrow in plain English and it generates the configuration.
* Deploy to testnet first (chain ID 50025, RPC: `https://evm-rpc-testnet.bitbadges.io`).
* See the [Cosmos Coin Wrapper Tutorial](cosmos-coin-wrapper-example.md) for more on IBC-backed tokens.
* Review [Building Your Collection Approvals](building-collection-approvals.md) for advanced approval patterns.

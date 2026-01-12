# IBC Backed Minting Invariants

IBC Backed Paths enable bidirectional conversion between our standard and IBC coins (standard Cosmos SDK coins). This creates a special address that acts as an intermediary, allowing tokens created with our standard to be "backed" by IBC-denominated tokens for seamless interoperability with the broader Cosmos ecosystem.

This can be very useful for use cases like reverse-wrapping IBC (ICS20) tokens with added compliance.

## Core Concept

An IBC Backed Path creates a special address that converts our standard tokens to and from IBC coins:

-   **Back tokens**: Send our standard tokens to the special address → receive IBC coins
-   **Unback tokens**: Send IBC coins to the special address → receive our standard tokens

```typescript
// Collection with IBC backed path
const collection: MsgCreateCollection = {
    creator: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    collectionId: '0', // 0 for new collection
    validTokenIds: [{ start: 1n, end: 1n }],
    invariants: {
        cosmosCoinBackedPath: {
            // address: auto-generated from conversion.sideA.denom
            conversion: {
                sideA: {
                    amount: '1000000', // IBC coin amount (from old ibcAmount)
                    denom: 'ibc/1234567890ABCDEF', // IBC denomination (from old ibcDenom)
                },
                sideB: [
                    {
                        amount: 1n,
                        tokenIds: [{ start: 1n, end: 1n }],
                        ownershipTimes: [
                            { start: 1n, end: 18446744073709551615n },
                        ],
                    },
                ],
            },
        },
        noCustomOwnershipTimes: false,
        maxSupplyPerId: '0',
        noForcefulPostMintTransfers: false,
        disablePoolCreation: false,
    },
    // ... other fields (collectionPermissions, manager, etc.)
};
```

## Special Address

Each IBC backed path has a **special address** automatically generated from the IBC denomination in `conversion.sideA.denom`:

```typescript
import { generateAliasAddressForIBCDenom } from 'bitbadgesjs-sdk';

const ibcDenom = 'ibc/1234567890ABCDEF';
const specialAddress = generateAliasAddressForIBCDenom(ibcDenom);
console.log('Special Address:', specialAddress);
```

**Properties:**

-   Deterministically derived from the IBC denom using a hash function
-   Acts as the intermediary for all conversions
-   Marked as a reserved protocol address
-   Holds the IBC coins that back the badge tokens

## Conversion Mechanism

The conversion uses a structured `Conversion` format that combines the IBC denom and amount into `sideA`, with badge tokens in `sideB`. You can't fractionalize it, but if you make the denominations as small as possible, you can get as fine-grained as you want.

```
conversion.sideA (amount + denom) = conversion.sideB[] (x/badges)
```

**Example:**

```typescript
// Configuration
const backedPath = {
    conversion: {
        sideA: {
            amount: '1000000', // IBC coin amount (from old ibcAmount)
            denom: 'ibc/1234567890ABCDEF', // IBC denomination (from old ibcDenom)
        },
        sideB: [
            {
                amount: 1n,
                tokenIds: [{ start: 1n, end: 1n }],
                ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            },
        ],
    },
};
```

**Conversion Structure:**

-   **`Conversion`** (with denom): Used by backed paths because the denom is part of the conversion
-   **`sideA`**: Contains both `amount` and `denom` (from old `ibcAmount` and `ibcDenom` fields)
-   **`sideB`**: Array of `Balance` objects that define the badge tokens (from old `balances` field)
-   **Conversion rate**: `conversion.sideA.amount` of `conversion.sideA.denom` = `conversion.sideB[]` tokens

## Configuration

IBC backed paths are configured as **collection invariants**, meaning:

-   Set only during collection creation
-   Cannot be modified after creation
-   Only **one** backed path allowed per collection

```typescript
const collection: MsgCreateCollection = {
    creator: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    collectionId: '0', // 0 for new collection
    validTokenIds: [{ start: 1n, end: 100n }],
    invariants: {
        cosmosCoinBackedPath: {
            conversion: {
                sideA: {
                    amount: '1000000', // IBC coin amount
                    denom: 'ibc/1234567890ABCDEF', // IBC denomination
                },
                sideB: [
                    {
                        amount: 1n,
                        tokenIds: [{ start: 1n, end: 100n }],
                        ownershipTimes: [
                            { start: 1n, end: 18446744073709551615n },
                        ],
                    },
                ],
            },
        },
        noCustomOwnershipTimes: false,
        maxSupplyPerId: '0',
        noForcefulPostMintTransfers: false,
        disablePoolCreation: false,
    },
    collectionPermissions: {
        // ... permission fields
    },
    manager: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    // ... other collection fields
};
```

## Mint Address Restrictions

When an IBC backed path is configured:

-   Transfers **from** the Mint address (mints) are never allowed
-   All mints must be done through the IBC backed path
-   Collection approvals cannot include the Mint address in `fromListId`

This prevents minting tokens that would bypass the backing mechanism and cause desyncs.

If you want a more hybrid approach or more customization, don't use the invariant and rather implement it with custom transferability logic instead.

```typescript
// ❌ Invalid - Cannot use Mint address with IBC backed path
const invalidApproval = {
    fromListId: 'Mint', // Not allowed when cosmosCoinBackedPath is set
    toListId: 'All',
    // ...
};

// ✅ Valid - Must use special address for minting
const validApproval = {
    fromListId: specialAddress, // Use the IBC backed path address
    toListId: 'All',
    // ...
};
```

## Transferability Requirements

The special address is subject to the same transferability requirements as any other address. You can user-gate, rate-limit, or apply any approval logic.

**Important:** Collection approvals used for IBC backed path operations must have `allowedBackedMinting: true` set in their `approvalCriteria`. This flag ensures that only explicitly designated approvals can be used for backed minting operations.

```typescript
// Example: Rate-limited backing
const collectionApprovals = [
    {
        fromListId: specialAddress,
        toListId: 'All',
        initiatedByListId: 'All',
        transferTimes: [{ start: 1n, end: 18446744073709551615n }],
        tokenIds: [{ start: 1n, end: 100n }],
        ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        approvalId: 'backing-approval',
        version: 0n,
        approvalCriteria: {
            allowedBackedMinting: true, // Required for IBC backed path operations
            maxNumTransfers: {
                perInitiatedByAddressMaxNumTransfers: 10n, // 10 backs per day
                // ... reset time intervals
            },
        },
    },
];
```

## Technical Implementation

### Address Detection

The system automatically detects transfers involving the special address:

-   Checks if `to` address matches the backed path address (backing)
-   Checks if `from` address matches the backed path address (unbacking)
-   Triggers the appropriate conversion logic

**Important:** Detection happens in `MsgTransferTokens` implementation. Using the bank module alone won't work because it doesn't know about IBC backed paths. To trigger a minting or unminting, you simply wire up a `MsgTransferTokens` message to the special address. Approvals of the special address are handled behind the scenes by the system. Note: The approval must be prioritized as this is a special context / environment. We also verify that the initiator is equal to the recipient / sender. There is no doing this on behalf of another user.

## Example: Backing Tokens

```typescript
// User sends badge tokens to special address
// ⚠️ IMPORTANT: IBC backed path operations require prioritized approvals (not compatible with auto-scan mode)
// The initiator must equal the sender/recipient - no doing this on behalf of another user
const backTokens: MsgTransferTokens = {
    creator: 'bb1user...', // Must equal from address
    collectionId: '1',
    transfers: [
        {
            from: 'bb1user...', // Must equal creator
            toAddresses: [specialAddress], // Special IBC backed path address
            balances: [
                {
                    amount: 5n,
                    tokenIds: [{ start: 1n, end: 1n }],
                    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'backing-approval',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: 0n,
                },
            ],
            onlyCheckPrioritizedCollectionApprovals: true,
        },
    ],
};

// Result: User receives corresponding IBC coins automatically based on the conversion rate
```

## Example: Unbacking Tokens

Approvals are actually handled behind the scenes by the system for the special address. You do not need to do anything special here besides having sufficient balances to unback the tokens.

```typescript
// User initiates transfer on behalf of special address to receive tokens
// ⚠️ IMPORTANT: IBC backed path operations require prioritized approvals (not compatible with auto-scan mode)
// The initiator must equal the recipient - no doing this on behalf of another user
const unbackTokens: MsgTransferTokens = {
    creator: 'bb1user...', // Must equal toAddress
    collectionId: '1',
    transfers: [
        {
            from: specialAddress, // Transfer from special IBC backed path address
            toAddresses: ['bb1user...'], // Must equal creator
            balances: [
                {
                    amount: 5n,
                    tokenIds: [{ start: 1n, end: 1n }],
                    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'unbacking-approval',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: 0n,
                },
            ],
            onlyCheckPrioritizedCollectionApprovals: true,
        },
    ],
};

// Result: User receives 5 badge tokens
// Corresponding IBC coins are deducted from special address
```

## Differences from Wrapper Paths

| Feature              | IBC Backed Path                        | Wrapper Path                 |
| -------------------- | -------------------------------------- | ---------------------------- |
| **Minting**          | No minting/burning (uses existing IBC) | Minting/burning of new denom |
| **Denom Source**     | Existing IBC denom                     | Generated denom              |
| **Configuration**    | Collection invariant                   | Can add paths, no edits      |
| **Standard Minting** | Disabled                               | Enabled                      |

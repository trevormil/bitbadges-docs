# Cosmos Coin Wrapper Paths

Cosmos Wrapper Paths enable wrapping between BitBadges tokens and native Cosmos SDK coin (x/bank) asset types, making tokens IBC-compatible. These paths automatically mint and burn tokens when transferring to/from specific wrapper addresses.

This is used to generate a 1:1 compatible mapping between our standard tokens and native Cosmos SDK coins. Note that this does not use an existing IBC denom, but rather creates a custom generated denomination.

Use cases could include:

-   Using our standard tokens for time-dependent logic but eventually making them native Cosmos SDK coins
-   Compatibility with existing Cosmos services and chains like Osmosis, Juno, etc.

> **Important**: Since wrapper addresses are uncontrollable (no private keys), approval design requires careful consideration. You must override the wrapper address's user-level approvals where necessary using collection approvals to ensure wrapping/unwrapping functions properly. Additionally, collection approvals used for wrapper path operations must have `allowSpecialWrapping: true` set in their `approvalCriteria`. See [Special Address Flags](../token-standard/learn/approval-criteria/special-address-flags.md) for details.

## Core Concept

Wrapper paths create special addresses that convert our standard tokens to native Cosmos SDK coins:

-   **Wrapping**: Send our standard tokens to wrapper address → receive native coins (tokens are burned, x/bank coins are minted)
-   **Unwrapping**: Send native coins to wrapper address → receive our standard tokens (x/bank coins are burned, tokens are minted)

```typescript
// Collection with wrapper path
const collection: MsgCreateCollection = {
    creator: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    collectionId: '0', // 0 for new collection
    validTokenIds: [{ start: 1n, end: 100n }],
    cosmosCoinWrapperPathsToAdd: [
        {
            denom: 'utoken',
            conversion: {
                sideA: {
                    amount: '1', // Required: amount of wrapped coin
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
            symbol: 'TOKEN',
            denomUnits: [
                {
                    decimals: 6n,
                    symbol: 'TOKEN',
                    isDefaultDisplay: true,
                },
            ],
            allowOverrideWithAnyValidToken: false,
            metadata: { uri: '', customData: '' }, // Optional metadata
        },
    ],
    aliasPathsToAdd: [
        {
            denom: 'utoken-alias',
            conversion: {
                sideA: {
                    amount: '1', // Required: amount of wrapped coin
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
            symbol: 'ALIAS',
            denomUnits: [
                {
                    decimals: 6n,
                    symbol: 'ALIAS',
                    isDefaultDisplay: true,
                },
            ],
            metadata: { uri: '', customData: '' }, // Optional metadata
        },
    ],
    // ... other fields
};
```

## Wrapper Paths vs Alias Paths

The system now distinguishes between two separate path types:

### Cosmos Coin Wrapper Paths

Used for actual wrapping/unwrapping with minting and burning:

-   **Purpose**: Convert tokens to native Cosmos SDK coins and vice versa
-   **Behavior**: Tokens are burned when wrapping, coins are minted. Coins are burned when unwrapping, tokens are minted
-   **Use case**: IBC transfers, converting tokens to native coins for Cosmos ecosystem compatibility
-   **Storage**: Stored in `cosmosCoinWrapperPaths` array
-   **Features**: Includes `address` field (wrapper address) and `allowOverrideWithAnyValidToken` option

```typescript
{
    denom: 'utoken',
            conversion: {
                sideA: {
                    amount: '1', // Required: amount of wrapped coin
                },
        sideB: [
            {
                amount: 1n,
                tokenIds: [{ start: 1n, end: 100n }],
                ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            },
        ],
    },
    symbol: 'TOKEN',
    denomUnits: [
        {
            decimals: 6n,
            symbol: 'TOKEN',
            isDefaultDisplay: true,
        },
    ],
    allowOverrideWithAnyValidToken: false,
    metadata: { uri: '', customData: '' }, // Optional PathMetadata
}
```

### Alias Paths

Used for compatibility with existing Cosmos SDK interfaces without actual wrapping:

-   **Purpose**: Alias denomination support (e.g., `badgeslp:COLLECTION_ID:denom`)
-   **Behavior**: No minting/burning occurs, only an alias for information purposes
-   **Use case**: Compatibility with liquidity pools, DeFi protocols that expect `sdk.Coin` format
-   **Storage**: Stored in `aliasPaths` array (separate from wrapper paths)
-   **See**: [Alias Compatibility](alias-compatibility.md) for details
-   **Note**: Does not include `address` or `allowOverrideWithAnyValidToken` fields

```typescript
{
    denom: 'utoken',
            conversion: {
                sideA: {
                    amount: '1', // Required: amount of alias unit
                },
        sideB: [
            {
                amount: 1n,
                tokenIds: [{ start: 1n, end: 100n }],
                ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            },
        ],
    },
    symbol: 'TOKEN',
    denomUnits: [
        {
            decimals: 6n,
            symbol: 'TOKEN',
            isDefaultDisplay: true,
        },
    ],
    metadata: { uri: '', customData: '' }, // Optional PathMetadata
}
```

## Wrapper Address Generation

Wrapper addresses are auto-generated based on the denom:

```typescript
import { generateAliasAddressForDenom } from 'bitbadgesjs-sdk';

const denom = 'utoken';
const wrapperAddress = generateAliasAddressForDenom(denom);
console.log('Wrapper Address:', wrapperAddress);
```

**Note:** The address is generated from the custom denom, not the full `badges:collectionId:denom` format.

## Conversion Structure

Wrapper paths and alias paths use a structured conversion format to define the relationship between wrapped/alias units and badge tokens:

### ConversionWithoutDenom

Used by wrapper paths and alias paths (denom stored separately):

```typescript
{
    conversion: {
        sideA: {
            amount: '1', // Required: amount of wrapped/alias coin (Uint type)
        },
        sideB: [
            // Balances[] that define which tokens participate
            {
                amount: 1n,
                tokenIds: [{ start: 1n, end: 100n }],
                ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            },
        ],
    },
}
```

**Key Points:**

-   `sideA.amount`: The amount of wrapped/alias coin units (required, must be specified)
-   `sideB`: Array of `Balance` objects that define the tokens involved in the conversion
-   The denom is stored at the path level, not in the conversion (hence "WithoutDenom")
-   Conversion rate: `sideA.amount` wrapped/alias units = `sideB[]` tokens

**Example:**

-   If `sideA.amount = "1"` and `sideB = [{ amount: 1n, tokenIds: [...], ... }]`
-   Then: `1 wrapped coin = 1 token` (1:1 conversion)

**Example with different rate:**

-   If `sideA.amount = "100"` and `sideB = [{ amount: 1n, tokenIds: [...], ... }]`
-   Then: `100 wrapped coins = 1 token` (100:1 conversion)

## Configuration Fields

### Denom

The base denomination for the wrapped coin. The full Cosmos denomination will be `badges:collectionId:denom`. Note that `badges:` is different from the `badgeslp:` format used for aliases elsewhere.

```typescript
{
    denom: 'utoken', // Base denom
    // Full denom: badges:1:utoken
}
```

### Conversion

Defines the conversion rate between wrapped coins and tokens using a structured conversion format:

```typescript
{
    conversion: {
        sideA: {
            amount: '1', // Required: amount of wrapped coin
        },
        sideB: [
            {
                amount: 1n, // Token amount
                tokenIds: [{ start: 1n, end: 100n }], // Token IDs that can wrap
                ownershipTimes: [{ start: 1n, end: 18446744073709551615n }], // Ownership times
            },
        ],
    },
}
```

**Conversion rate:** `conversion.sideA.amount wrapped coin = conversion.sideB[] tokens`

**Key Points:**

-   `sideA.amount` is required and must be specified (cannot be "0" or nil)
-   `sideB` contains the `Balances[]` that define which tokens participate in wrapping
-   The denom is stored separately at the path level (not in the conversion)

### Denomination Units

Multiple denomination units allow different display formats:

```typescript
{
    denomUnits: [
        {
            decimals: 3n, // 3 decimal places
            symbol: 'mtoken', // Milli-token
            isDefaultDisplay: false,
            metadata: { uri: '', customData: '' }, // Optional PathMetadata
        },
        {
            decimals: 6n, // 6 decimal places
            symbol: 'TOKEN', // Full token
            isDefaultDisplay: true, // Shown by default
            metadata: { uri: '', customData: '' }, // Optional PathMetadata
        },
    ],
}
```

**System:**

-   `utoken` = base unit (0 decimals)
-   `mtoken` = 1,000 `utoken` (3 decimals)
-   `TOKEN` = 1,000,000 `utoken` (6 decimals, default display)

**Note:** Each `DenomUnit` now includes an optional `metadata` field of type `PathMetadata` for additional information.

### Allow Override With Any Valid Token

When `true`, allows the wrapper to accept any SINGLE valid token ID from the collection's `validTokenIds` range:

```typescript
{
    denom: 'utoken',
    conversion: {
        sideA: {
            amount: '1',
        },
        sideB: [
            {
                amount: 1n,
                tokenIds: [{ start: 1n, end: 1n }], // Overridden during transfer
                ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            },
        ],
    },
    allowOverrideWithAnyValidToken: true, // Accept any valid token ID
}
```

**How it works:**

1. User transfers token ID 5 to wrapper
2. System validates token ID 5 is in `validTokenIds`
3. System temporarily overrides `conversion.sideB[].tokenIds` with `[{ start: 5n, end: 5n }]` ignoring the values set in the `sideB` array
4. Conversion proceeds with token ID 5

## {id} Placeholder Support

You can use `{id}` in the denom to dynamically replace it with the actual token ID:

```typescript
{
    denom: 'utoken{id}', // Dynamic denom
    symbol: 'TOKEN:{id}',
    conversion: {
        sideA: {
            amount: '1',
        },
        sideB: [
            {
                amount: 1n,
                tokenIds: [{ start: 1n, end: 1n }],
                ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
            },
        ],
    },
    allowOverrideWithAnyValidToken: true,
}
```

**Example:** Transferring token ID 5 results in denom `utoken5`.

### Metadata

Both wrapper paths and alias paths support optional metadata using the standard metadata structure:

```typescript
{
    metadata: {
        uri: 'ipfs://Qm...', // Optional URI to hosted JSON metadata
        customData: '{"key": "value"}', // Optional custom JSON data
    },
}
```

The hosted JSON at the URI typically contains `{ name, image, description }`, though the image is the primary use case. The on-chain `symbol` field is used for identification, not the metadata name.

**Note:** Metadata is optional. It's also available on `DenomUnit` objects for additional per-unit metadata.

## Transferability Requirements

Wrapper addresses are subject to the same transferability requirements as any other address. You can user-gate, rate-limit, or apply custom logic:

```typescript
// Example: Rate-limited wrapping
const collectionApprovals = [
    {
        fromListId: 'AllWithoutMint',
        toListId: wrapperAddress,
        initiatedByListId: 'All',
        transferTimes: [{ start: 1n, end: 18446744073709551615n }],
        tokenIds: [{ start: 1n, end: 100n }],
        ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        approvalId: 'wrap-approval',
        version: 0n,
        approvalCriteria: {
            allowSpecialWrapping: true, // Required for wrapper path operations
            maxNumTransfers: {
                perInitiatedByAddressMaxNumTransfers: 10n, // 10 wraps per day
                // ... reset time intervals
            },
        },
    },
    {
        fromListId: wrapperAddress,
        toListId: 'All',
        initiatedByListId: 'All',
        transferTimes: [{ start: 1n, end: 18446744073709551615n }],
        tokenIds: [{ start: 1n, end: 100n }],
        ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        approvalId: 'unwrap-approval',
        version: 0n,
        approvalCriteria: {
            allowSpecialWrapping: true, // Required for wrapper path operations
            // Override wrapper's incoming approvals
            overridesToIncomingApprovals: true,
        },
    },
];
```

## Conversion Process

### Token to Coin (Wrapping)

1. User transfers tokens to wrapper address
2. System processes denom (replaces `{id}` if present, validates override if enabled)
3. System burns tokens from user's balance
4. System mints equivalent native coins
5. Coins credited to user's account

```typescript
// Wrapping tokens
// ⚠️ IMPORTANT: Wrapping/unwrapping requires prioritized approvals (not compatible with auto-scan mode)
const wrapTokens: MsgTransferTokens = {
    creator: 'bb1user...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1user...',
            toAddresses: [wrapperAddress],
            balances: [
                {
                    amount: 10n,
                    tokenIds: [{ start: 1n, end: 100n }],
                    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'wrap-approval',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: 0n,
                },
            ],
            onlyCheckPrioritizedCollectionApprovals: true,
        },
    ],
};

// Result: User receives 10 badges:1:utoken coins (based on conversion.sideA.amount = 1)
// 10 badge tokens are burned (based on conversion.sideB balances)
```

### Coin to Token (Unwrapping)

Unwrapping still uses `MsgTransferTokens`. You initiate a transfer on behalf of the wrapper address:

1. User initiates `MsgTransferTokens` with wrapper address as `from`
2. System processes denom (replaces `{id}` if present, validates override if enabled)
3. System burns native coins from wrapper address
4. System mints equivalent tokens
5. Tokens credited to user's balance

```typescript
// Unwrapping coins
// ⚠️ IMPORTANT: Wrapping/unwrapping requires prioritized approvals (not compatible with auto-scan mode)
// You initiate a transfer on behalf of the wrapper address
const unwrapCoins: MsgTransferTokens = {
    creator: 'bb1user...',
    collectionId: '1',
    transfers: [
        {
            from: wrapperAddress, // Transfer from wrapper address
            toAddresses: ['bb1user...'], // To user
            balances: [
                {
                    amount: 10n,
                    tokenIds: [{ start: 1n, end: 100n }],
                    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'unwrap-approval',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: 0n,
                },
            ],
            onlyCheckPrioritizedCollectionApprovals: true,
        },
    ],
};

// Result: User receives 10 badge tokens (based on conversion.sideB balances)
// 10 badges:1:utoken coins are burned from wrapper address (based on conversion.sideA.amount = 1)
```

## Use Cases

### IBC Transfers

Enable cross-chain transfers of wrapped tokens:

```typescript
// Wrap tokens for IBC transfer
// ⚠️ IMPORTANT: Requires prioritized approvals
// The conversion rate is defined in the wrapper path's conversion field
const wrapForIBC: MsgTransferTokens = {
    creator: 'bb1user...',
    collectionId: '1',
    transfers: [
        {
            from: 'bb1user...',
            toAddresses: [wrapperAddress],
            balances: [
                {
                    amount: 100n,
                    tokenIds: [{ start: 1n, end: 100n }],
                    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
                },
            ],
            prioritizedApprovals: [
                {
                    approvalId: 'wrap-approval',
                    approvalLevel: 'collection',
                    approverAddress: '',
                    version: 0n,
                },
            ],
            onlyCheckPrioritizedCollectionApprovals: true,
        },
    ],
};

// Transfer wrapped coins via IBC
const ibcTransfer = {
    sourcePort: 'transfer',
    sourceChannel: 'channel-0',
    token: {
        denom: 'badges:1:utoken',
        amount: '100',
    },
    sender: 'bb1user...',
    receiver: 'cosmos1...',
};
```

### DeFi Integration

Use wrapped tokens in Cosmos DeFi protocols:

```typescript
// Add wrapped tokens to liquidity pool
const addLiquidity = {
    poolId: '1',
    sender: 'bb1user...',
    tokenInMaxs: [
        {
            denom: 'badges:1:utoken',
            amount: '1000000',
        },
        {
            denom: 'uatom',
            amount: '500000',
        },
    ],
};
```

## Permission Control

Adding new cosmos coin wrapper paths to a collection is controlled by the `canAddMoreCosmosCoinWrapperPaths` permission. This permission allows you to control when managers can add new wrapper paths.

### Default Behavior

-   **Empty/Nil Permissions**: When `canAddMoreCosmosCoinWrapperPaths` is empty or nil, adding paths is **allowed** (neutral state)
-   **Migration**: Collections migrated from v21 will have empty permissions, meaning adding paths is allowed by default

### Permission Structure

The permission uses the `ActionPermission` type with time-based controls:

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canAddMoreCosmosCoinWrapperPaths: [
        {
            permanentlyPermittedTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
            permanentlyForbiddenTimes: [],
        },
    ],
};
```

### Usage Examples

**Allow adding paths at all times:**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canAddMoreCosmosCoinWrapperPaths: [], // Empty = allowed by default
};
```

**Lock adding paths forever:**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canAddMoreCosmosCoinWrapperPaths: [
        {
            permanentlyPermittedTimes: [],
            permanentlyForbiddenTimes: [
                { start: 1n, end: 18446744073709551615n },
            ],
        },
    ],
};
```

**Allow adding paths only during specific period:**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canAddMoreCosmosCoinWrapperPaths: [
        {
            permanentlyPermittedTimes: [
                { start: 1704067200000n, end: 1735689600000n },
            ],
            permanentlyForbiddenTimes: [],
        },
    ],
};
```

### Permission Check

When using `MsgUniversalUpdateCollection` to add wrapper paths via `cosmosCoinWrapperPathsToAdd`, the system checks the `canAddMoreCosmosCoinWrapperPaths` permission before processing the paths. If the permission check fails, the transaction will be rejected with an appropriate error message.

**Note**: The permission is checked before paths are added, but the permission itself can be updated at the end of the transaction (if `updateCollectionPermissions` is set to `true`).

## Differences from IBC Backed Paths

| Feature           | Wrapper Path                 | IBC Backed Path                        |
| ----------------- | ---------------------------- | -------------------------------------- |
| **Minting**       | Minting/burning of new denom | No minting/burning (uses existing IBC) |
| **Denom Source**  | Generated denom              | Existing IBC denom                     |
| **Configuration** | Can add paths, no edits      | Collection invariant                   |
| **Mint Address**  | Enabled                      | Disabled                               |

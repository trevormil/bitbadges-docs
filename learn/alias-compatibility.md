# Alias Compatibility

In many instances, you may see BitBadges provide alias denomination support for compatibility with existing Cosmos SDK interfaces like `sdk.Coin`. This enables seamless integration with liquidity pools, multi-standard environments, and other systems that expect standard Cosmos coin formats (denom, amount).

Note that the environment must support aliases for this to work.

## Alias Denomination Format

BitBadges uses the format `badgeslp:COLLECTION_ID:denom` for alias denominations:

-   **Format**: `badgeslp:COLLECTION_ID:denom`
-   **Example**: `5 badgeslp:73:utoken`
    -   Collection ID: `73`
    -   Base denomination: `utoken` (from the collection's `aliasPaths` array)
    -   Amount: `5`

## How It Works

The alias denomination converts an integer amount to `Balances[]` using the collection's `aliasPaths` field, which defines the conversion rate.

### Conversion Process

1. **Parse the alias**: Extract collection ID and denom from `badgeslp:COLLECTION_ID:denom`
2. **Find alias path**: Look up the matching `AliasPath` in the collection's `aliasPaths` array by denom
3. **Convert amount**: Use the path's `conversion` field to convert the integer amount to `Balances[]`. The conversion rate is: `conversion.sideA.amount` alias units = `conversion.sideB[]` tokens. For example, if `sideA.amount = "1"` and `sideB = [{ amount: 1n, ... }]`, then `1 badgeslp:73:utoken = 1 token` (1:1 conversion)
4. **Execute transfer**: Process the transfer using the converted `Balances[]` via `MsgTransferTokens`

### Important Notes

-   **No wrapping involved**: This is not a wrapping/unwrapping process. The conversion is simply an alias for the full `Balances[]` field.
-   **Conversion rate defined**: The conversion rate is defined in the collection's `aliasPaths` field, specifically in the `conversion.sideA.amount` and `conversion.sideB[]` fields of each path.
-   **Auto-scan mode**: Implementations that support alias denominations almost always operate in **auto-scan mode** (no prioritized approvals required).

## Use Cases

### Liquidity Pool Environments

Alias denominations enable BitBadges tokens to participate in liquidity pools that expect standard `sdk.Coin` formats:

```typescript
// Example: Adding liquidity to a pool
const coins = [
    {
        denom: 'badgeslp:73:utoken',
        amount: '1000000', // Converts to Balances[] via aliasPaths behind the scenes
    },
    {
        denom: 'uatom',
        amount: '500000',
    },
];
```

### Multi-Standard Support

In environments where you need to support multiple token standards, alias denominations provide a unified interface:

```typescript
// Works seamlessly with standard Cosmos SDK coins
const transfer = {
    from: 'bb1...',
    to: 'bb1...',
    amount: [
        {
            denom: 'badgeslp:73:utoken', // BitBadges token (alias)
            amount: '1000',
        },
        {
            denom: 'uatom', // Standard Cosmos SDK coin
            amount: '500',
        },
    ],
};
```

## Configuration

The conversion is defined in the collection's `aliasPaths` field:

```typescript
const collection: MsgCreateCollection = {
    // ... other fields
    aliasPathsToAdd: [
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
                        ownershipTimes: [
                            { start: 1n, end: 18446744073709551615n },
                        ],
                    },
                ],
            },
            symbol: 'BASETOKEN',
            denomUnits: [
                {
                    decimals: 6n,
                    symbol: 'TOKEN',
                    isDefaultDisplay: true,
                },
            ],
            metadata: { uri: '', customData: '' }, // Optional PathMetadata
        },
    ],
};
```

**Metadata**: Alias paths use the standard metadata structure with `uri` (e.g., `ipfs://Qm...`) pointing to hosted JSON containing `{ name, image, description }`. The image is the primary use case. The on-chain `symbol` field is typically used for identification, not the metadata name.

In this example:

-   `1 badgeslp:COLLECTION_ID:utoken` converts to `1` token with IDs `1-100` and full ownership times
-   The conversion rate is `1:1` (1 alias unit = 1 token) because `conversion.sideA.amount = "1"` and `conversion.sideB[0].amount = 1n`
-   The conversion structure uses `ConversionWithoutDenom` because the denom is stored separately at the path level

## Permission Control

Adding new alias paths to a collection is controlled by the `canAddMoreAliasPaths` permission. This permission allows you to control when managers can add new alias paths.

### Default Behavior

-   **Empty/Nil Permissions**: When `canAddMoreAliasPaths` is empty or nil, adding paths is **allowed** (neutral state)
-   **Migration**: Collections migrated from v21 will have empty permissions, meaning adding paths is allowed by default

### Permission Structure

The permission uses the `ActionPermission` type with time-based controls:

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canAddMoreAliasPaths: [
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
    canAddMoreAliasPaths: [], // Empty = allowed by default
};
```

**Lock adding paths forever:**

```typescript
const collectionPermissions: CollectionPermissions<bigint> = {
    canAddMoreAliasPaths: [
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
    canAddMoreAliasPaths: [
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

When using `MsgUniversalUpdateCollection` to add alias paths via `aliasPathsToAdd`, the system checks the `canAddMoreAliasPaths` permission before processing the paths. If the permission check fails, the transaction will be rejected with an appropriate error message.

**Note**: The permission is checked before paths are added, but the permission itself can be updated at the end of the transaction (if `updateCollectionPermissions` is set to `true`).

## Benefits

-   **Drop-In Replacement**: Upgrade existing systems to support BitBadges tokens with simply changing a line of code
-   **Seamless Integration**: Works with existing Cosmos SDK interfaces and tools
-   **Liquidity Pool Compatibility**: Enables participation in AMM pools and DeFi protocols
-   **Multi-Standard Support**: Unified interface for different token types
-   **No Wrapping Overhead**: Direct alias conversion without minting/burning

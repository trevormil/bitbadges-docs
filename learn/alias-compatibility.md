# Alias Compatibility

In many instances, you may see BitBadges provide alias denomination support for compatibility with existing Cosmos SDK interfaces like `sdk.Coin`. This enables seamless integration with liquidity pools, multi-standard environments, and other systems that expect standard Cosmos coin formats (denom, amount).

Note that the environment must support aliases for this to work.

## Alias Denomination Format

BitBadges uses the format `badgeslp:COLLECTION_ID:denom` for alias denominations:

-   **Format**: `badgeslp:COLLECTION_ID:denom`
-   **Example**: `5 badgeslp:73:utoken`
    -   Collection ID: `73`
    -   Base denomination: `utoken` (from the collection's `cosmosCoinWrapperPaths` path)
    -   Amount: `5`

## How It Works

The alias denomination converts an integer amount to `Balances[]` using the collection's `cosmosCoinWrapperPaths` field, which defines the conversion rate.

### Conversion Process

1. **Parse the alias**: Extract collection ID and denom from `badgeslp:COLLECTION_ID:denom`
2. **Find wrapper path**: Look up the matching `cosmosCoinWrapperPath` in the collection's `cosmosCoinWrapperPaths` array by denom
3. **Convert amount**: Use the path's `balances` field to convert the integer amount to `Balances[]. This is the conversion rate. 1 badgeslp:73:utoken = amount defined in balances
4. **Execute transfer**: Process the transfer using the converted `Balances[]` via `MsgTransferTokens`

### Important Notes

-   **No wrapping involved**: This is not a wrapping/unwrapping process. The conversion is simply an alias for the full `Balances[]` field.
-   **Conversion rate defined**: The conversion rate is defined in the collection's `cosmosCoinWrapperPaths` field, specifically in the `balances` array of each path.
-   **Auto-scan mode**: Implementations that support alias denominations almost always operate in **auto-scan mode** (no prioritized approvals required).

## Use Cases

### Liquidity Pool Environments

Alias denominations enable BitBadges tokens to participate in liquidity pools that expect standard `sdk.Coin` formats:

```typescript
// Example: Adding liquidity to a pool
const coins = [
    {
        denom: 'badgeslp:73:utoken',
        amount: '1000000', // Converts to Balances[] via cosmosCoinWrapperPaths behind the scenes
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

The conversion is defined in the collection's `cosmosCoinWrapperPaths` field:

```typescript
const collection: MsgCreateCollection = {
    // ... other fields
    cosmosCoinWrapperPaths: [
        {
            denom: 'utoken',
            balances: [
                {
                    amount: 1n,
                    tokenIds: [{ start: 1n, end: 100n }],
                    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
                },
            ],
            symbol: 'BASETOKEN',
            denomUnits: [
                {
                    decimals: 6n,
                    symbol: 'TOKEN',
                    isDefaultDisplay: true,
                },
            ],
            allowOverrideWithAnyValidToken: false,
        },
    ],
};
```

In this example:

-   `1 badgeslp:COLLECTION_ID:utoken` converts to `1` token with IDs `1-100` and full ownership times
-   The conversion rate is `1:1` (1 alias unit = 1 token)

## Benefits

-   **Drop-In Replacement**: Upgrade existing systems to support BitBadges tokens with simply changing a line of code
-   **Seamless Integration**: Works with existing Cosmos SDK interfaces and tools
-   **Liquidity Pool Compatibility**: Enables participation in AMM pools and DeFi protocols
-   **Multi-Standard Support**: Unified interface for different token types
-   **No Wrapping Overhead**: Direct alias conversion without minting/burning

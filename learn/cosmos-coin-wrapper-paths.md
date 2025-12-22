# Cosmos Coin Wrapper Paths

Cosmos Wrapper Paths enable wrapping between BitBadges tokens and native Cosmos SDK coin (x/bank) asset types, making tokens IBC-compatible. These paths automatically mint and burn tokens when transferring to/from specific wrapper addresses.

This is used to generate a 1:1 compatible mapping between our standard tokens and native Cosmos SDK coins. Note that this does not use an existing IBC denom, but rather creates a custom generated denomination.

Use cases could include:

-   Using our standard tokens for time-dependent logic but eventually making them native Cosmos SDK coins
-   Compatibility with existing Cosmos services and chains like Osmosis, Juno, etc.

> **Important**: Since wrapper addresses are uncontrollable (no private keys), approval design requires careful consideration. You must override the wrapper address's user-level approvals where necessary using collection approvals to ensure wrapping/unwrapping functions properly.

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
            balances: [
                {
                    amount: 1n,
                    tokenIds: [{ start: 1n, end: 100n }],
                    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
                },
            ],
            symbol: 'TOKEN',
            denomUnits: [
                {
                    decimals: 6n,
                    symbol: 'TOKEN',
                    isDefaultDisplay: true,
                },
            ],
            allowOverrideWithAnyValidToken: false,
            allowCosmosWrapping: true, // Enable wrapping/unwrapping
        },
    ],
    // ... other fields
};
```

## Alias-Only vs Wrappable Paths

Wrapper paths serve two distinct purposes based on the `allowCosmosWrapping` field:

### Alias-Only (`allowCosmosWrapping: false`)

Used for compatibility with existing Cosmos SDK interfaces without actual wrapping:

-   **Purpose**: Alias denomination support (e.g., `badgeslp:COLLECTION_ID:denom`)
-   **Behavior**: No minting/burning occurs, only an alias for information al prposes
-   **Use case**: Compatibility with liquidity pools, DeFi protocols that expect `sdk.Coin` format
-   **See**: [Alias Compatibility](alias-compatibility.md) for details

```typescript
{
    denom: 'utoken',
    balances: [
        {
            amount: 1n,
            tokenIds: [{ start: 1n, end: 100n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        },
    ],
    symbol: 'TOKEN',
    denomUnits: [
        {
            decimals: 6n,
            symbol: 'TOKEN',
            isDefaultDisplay: true,
        },
    ],
    allowCosmosWrapping: false, // Alias-only, no wrapping
}
```

### Wrappable/Convertable (`allowCosmosWrapping: true`)

Enables actual wrapping and unwrapping with minting/burning:

-   **Purpose**: Convert tokens to native Cosmos SDK coins and vice versa
-   **Behavior**: Tokens are burned when wrapping, coins are minted. Coins are burned when unwrapping, tokens are minted
-   **Use case**: IBC transfers, converting tokens to native coins for Cosmos ecosystem compatibility

```typescript
{
    denom: 'utoken',
    balances: [
        {
            amount: 1n,
            tokenIds: [{ start: 1n, end: 100n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        },
    ],
    symbol: 'TOKEN',
    denomUnits: [
        {
            decimals: 6n,
            symbol: 'TOKEN',
            isDefaultDisplay: true,
        },
    ],
    allowCosmosWrapping: true, // Enable wrapping/unwrapping
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

## Configuration Fields

### Denom

The base denomination for the wrapped coin. The full Cosmos denomination will be `badges:collectionId:denom`. Note that `badges:` is different from the `badgeslp:` format used for aliases elsewhere.

```typescript
{
    denom: 'utoken', // Base denom
    // Full denom: badges:1:utoken
}
```

### Balances

Defines the conversion rate and which tokens participate in wrapping:

```typescript
{
    balances: [
        {
            amount: 1n, // 1 wrapped coin unit
            tokenIds: [{ start: 1n, end: 100n }], // Token IDs that can wrap
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }], // Ownership times
        },
    ],
}
```

**Conversion rate:** `1 wrapped coin = balances[] tokens`

### Denomination Units

Multiple denomination units allow different display formats:

```typescript
{
    denomUnits: [
        {
            decimals: 3n, // 3 decimal places
            symbol: 'mtoken', // Milli-token
            isDefaultDisplay: false,
        },
        {
            decimals: 6n, // 6 decimal places
            symbol: 'TOKEN', // Full token
            isDefaultDisplay: true, // Shown by default
        },
    ],
}
```

**System:**

-   `utoken` = base unit (0 decimals)
-   `mtoken` = 1,000 `utoken` (3 decimals)
-   `TOKEN` = 1,000,000 `utoken` (6 decimals, default display)

### Allow Override With Any Valid Token

When `true`, allows the wrapper to accept any SINGLE valid token ID from the collection's `validTokenIds` range:

```typescript
{
    denom: 'utoken',
    balances: [
        {
            amount: 1n,
            tokenIds: [{ start: 1n, end: 1n }], // Overridden during transfer
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        },
    ],
    allowOverrideWithAnyValidToken: true, // Accept any valid token ID
}
```

**How it works:**

1. User transfers token ID 5 to wrapper
2. System validates token ID 5 is in `validTokenIds`
3. System temporarily overrides `balances[].tokenIds` with `[{ start: 5n, end: 5n }]` ignoring the values set in the `balances` array
4. Conversion proceeds with token ID 5

## {id} Placeholder Support

You can use `{id}` in the denom to dynamically replace it with the actual token ID:

```typescript
{
    denom: 'utoken{id}', // Dynamic denom
    symbol: 'TOKEN:{id}',
    balances: [
        {
            amount: 1n,
            tokenIds: [{ start: 1n, end: 1n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        },
    ],
    allowOverrideWithAnyValidToken: true,
}
```

**Example:** Transferring token ID 5 results in denom `utoken5`.

### Allow Cosmos Wrapping

The `allowCosmosWrapping` field determines the purpose of the wrapper path:

**`allowCosmosWrapping: false` (Alias-Only)**

-   Used for alias denomination compatibility (e.g., `badgeslp:COLLECTION_ID:denom`)
-   No actual wrapping/unwrapping occurs
-   No minting/burning of coins
-   Simply provides a conversion mapping for compatibility with existing interfaces
-   See [Alias Compatibility](alias-compatibility.md) for details

**`allowCosmosWrapping: true` (Wrappable/Convertable)**

-   Enables actual wrapping and unwrapping functionality
-   Tokens are burned when wrapping, coins are minted
-   Coins are burned when unwrapping, tokens are minted
-   Required for IBC transfers and full Cosmos ecosystem compatibility

```typescript
// Alias-only path (no wrapping)
{
    denom: 'utoken',
    balances: [
        {
            amount: 1n,
            tokenIds: [{ start: 1n, end: 100n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        },
    ],
    symbol: 'TOKEN',
    denomUnits: [
        {
            decimals: 6n,
            symbol: 'TOKEN',
            isDefaultDisplay: true,
        },
    ],
    allowCosmosWrapping: false, // Alias-only
}

// Wrappable path (with wrapping/unwrapping)
{
    denom: 'utoken',
    balances: [
        {
            amount: 1n,
            tokenIds: [{ start: 1n, end: 100n }],
            ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        },
    ],
    symbol: 'TOKEN',
    denomUnits: [
        {
            decimals: 6n,
            symbol: 'TOKEN',
            isDefaultDisplay: true,
        },
    ],
    allowCosmosWrapping: true, // Enable wrapping/unwrapping
}
```

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

// Result: User receives 10 badges:1:utoken coins
// 10 badge tokens are burned
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

// Result: User receives 10 badge tokens
// 10 badges:1:utoken coins are burned from wrapper address
```

## Use Cases

### IBC Transfers

Enable cross-chain transfers of wrapped tokens:

```typescript
// Wrap tokens for IBC transfer
// ⚠️ IMPORTANT: Requires prioritized approvals
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

## Differences from IBC Backed Paths

| Feature           | Wrapper Path                 | IBC Backed Path                        |
| ----------------- | ---------------------------- | -------------------------------------- |
| **Minting**       | Minting/burning of new denom | No minting/burning (uses existing IBC) |
| **Denom Source**  | Generated denom              | Existing IBC denom                     |
| **Configuration** | Timeline-based (mutable)     | Collection invariant (immutable)       |
| **Mint Address**  | Enabled                      | Disabled                               |

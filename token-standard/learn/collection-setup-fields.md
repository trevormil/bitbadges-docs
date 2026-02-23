# Collection Configuration

There are plenty of fields to define the core configuration for your collection during creation. These are all stored on the collection object controlled by the manager. These fields control metadata, standards, valid token IDs, custom data, and immutable invariants.

Most fields are updatable over time, according to their corresponding permissions set in the collection permissions object (e.g. canUpdateValidTokenIds, canUpdateCollectionMetadata, etc). However, some fields are immutable and cannot be changed after creation (invariants).

## Complete Example

```typescript
import { MsgCreateCollection } from 'bitbadgesjs-sdk';

const msg: MsgCreateCollection = {
    creator: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    defaultBalances: {
        balances: [],
        outgoingApprovals: [],
        incomingApprovals: [],
        autoApproveSelfInitiatedOutgoingTransfers: false,
        autoApproveSelfInitiatedIncomingTransfers: true,
        autoApproveAllIncomingTransfers: false,
        userPermissions: {
            // ... permission fields
        },
    },
    validTokenIds: [{ start: 1n, end: 100n }],
    collectionPermissions: {
        // ... permission fields
    },
    manager: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    collectionMetadata: {
        uri: 'ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub',
        customData: '',
    },
    tokenMetadata: [
        {
            uri: 'ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub/{id}',
            tokenIds: [{ start: 1n, end: 100n }],
            customData: '',
        },
    ],
    customData: 'Application-specific data',
    collectionApprovals: [
        // ... approval fields
    ],
    standards: ['Tradable', 'NFT'],
    isArchived: false,
    invariants: {
        noCustomOwnershipTimes: false,
        maxSupplyPerId: '0',
        cosmosCoinBackedPath: undefined,
        noForcefulPostMintTransfers: false,
        disablePoolCreation: false,
    },
    // ... other fields
};
```

## Valid Token IDs

`validTokenIds` defines the range of token IDs that exist within a collection. This is mainly informational but may also be used to enforce certain rules like canOverrideWithAnyValidTokenId.

Must be sequential from 1 to the total supply of the collection.

```typescript
const validTokenIds: UintRange<bigint>[] = [{ start: 1n, end: 100n }];
```

**Key points:**

-   Set during collection creation or via `MsgUpdateCollection`
-   Controlled by `canUpdateValidTokenIds` permission
-   Strongly recommended to be set at genesis and locked forever. Expanding / reducing valid token IDs is not recommended in most cases and advanced.

## Standards

`standards` provides informational tags that guide how to interpret and implement collection features. Standards are purely informational—no blockchain validation.

```typescript
const standards: string[] = ['Tradable', 'NFT', 'Cosmos Wrappable'];
```

**BitBadges Site Standards:**

-   **Tradable**: Enables trading interface, orderbook tracking, and marketplace features
-   **NFT**: Expects supply = 1 per token ID with full ownership times
-   **Cosmos Wrappable**: Can be wrapped into Cosmos SDK coin denominations
-   **Subscriptions**: Designed for recurring content delivery and subscription systems
-   **Quests**: Achievement-based systems and quest completion tracking

For more information on compatibility with the BitBadges site, please reach out.

**Important:** Standards are informational only. Applications must verify compliance.

## Collection Metadata

`collectionMetadata` defines metadata for the entire collection.

```typescript
const collectionMetadata: CollectionMetadata = {
    uri: 'ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub',
    customData: '',
};
```

**Metadata Format:** The BitBadges API expects metadata to follow this interface:

```typescript
interface Metadata {
    name: string;
    description: string;
    image: string;
}
```

**Permission Control:** Updates controlled by `canUpdateCollectionMetadata` permission.

## Token Metadata

`tokenMetadata` defines metadata for individual tokens. Uses first-match approach via linear scan for specific token IDs. We support the dynamic token ID replacement feature {id} in the URI.

```typescript
const tokenMetadata: TokenMetadata[] = [
    {
        uri: 'ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub/{id}',
        tokenIds: [{ start: 1n, end: 100n }],
        customData: '',
    },
];
```

**Key Features:**

-   **Dynamic Token ID Replacement**: URI `{id}` placeholder is replaced with actual token ID
-   **First-Match Logic**: Entries evaluated in order; first matching entry for a token ID is used, subsequent entries are ignored.
-   **Permission Control**: Updates controlled by `canUpdateTokenMetadata` permission

## Custom Data

`customData` provides generic string storage for application-specific information. Not used by BitBadges site—for future customization and extensibility.

This is often used for application-specific implementations or contract integrations. You may also see `customData` used elsewhere like in approvals, address lists, and other structures.

```typescript
const customData: string = 'Any string value you want to store';
```

**Where Custom Data Appears:**

-   Collection-level: `customData`
-   Token metadata: `customData` within token metadata structures
-   Address lists: `customData` for list-specific information
-   Messages: Various transaction messages include custom data fields

**Permission Control:** Updates controlled by `canUpdateCustomData` permission.

## Default Balances

`defaultBalances` are predefined balance stores automatically assigned to new users (uninitialized balance stores) when they first interact with a collection. Set during collection creation only—cannot be updated after genesis.

This is not often used but can be useful for certain use cases:

-   Blocking incoming transfers by default (enforcing opt-in only restrictions)
-   Default starting balances for all users
-   Default approval settings for all users

```typescript
const defaultBalances: UserBalanceStore<bigint> = {
    balances: [],
    outgoingApprovals: [],
    incomingApprovals: [],
    autoApproveSelfInitiatedOutgoingTransfers: false,
    autoApproveSelfInitiatedIncomingTransfers: true,
    autoApproveAllIncomingTransfers: false,
    userPermissions: {
        // ... permission fields
    },
};
```

**Important Limitations:**

-   **No complex approval criteria**: Cannot include any approvals not compatible with auto-scan mode. See [Auto-Scan and Prioritized Approvals](../../x-tokenization/learn/auto-scan-and-prioritized-approvals.md) for more information.

**Key points:**

-   Creation-only field—set once during collection creation
-   Establishes baseline behavior for all new users
-   Users can customize their own approval settings after initialization

## IsArchived

`isArchived` controls whether a collection is archived, temporarily or permanently disabling all transactions while keeping collection data verifiable and public on-chain.

This is a collection-level option that can be used to temporarily or permanently disable all transactions until unarchived. Controlled by the manager. Useful for pausing, halts, or other maintenance operations.

Note that there are also other ways to implement halting logic which may be better suited for your use case.

-   Chain-level x/circuit breakers
-   Dynamic stores / token ownership requirements that can be controlled by another entity

```typescript
const isArchived: boolean = false;
```

**Transaction Behavior:**

-   **When archived**: All transactions fail (no updates, transfers, or changes allowed). Read operations continue. Only unarchiving transactions can succeed.
-   **When unarchived**: Normal operations resume. All collection data remains intact. Standard permission checks apply.

**Permission Control:** Updates controlled by `canArchiveCollection` permission. Note that this permission controls the updatability of the `isArchived` field, not the current archive status. You can lock the archive status forever (either `true` or `false`) by permanently forbidding updates.

## Invariants

`invariants` are immutable rules set upon collection creation that cannot be broken or modified afterward. They enforce fundamental constraints on collection behavior.

```typescript
const invariants: CollectionInvariants<bigint> = {
    noCustomOwnershipTimes: false,
    maxSupplyPerId: '0',
    cosmosCoinBackedPath: undefined,
    noForcefulPostMintTransfers: false,
    disablePoolCreation: false,
    evmQueryChallenges: [],
};
```

### Available Invariants

**noCustomOwnershipTimes**

-   Enforces all ownership times must be full ranges `[{ start: 1n, end: 18446744073709551615n }]` for all time-dependent structures - balances, approvals, etc.
-   Prevents time-based restrictions on token ownership
-   Affects collection approvals, user approvals, and transfer balances
-   Typically recommended to be set to true if you do not need such functionality.

**maxSupplyPerId**

-   Maximum supply per token ID (string-based uint64)
-   Prevents any balance amount from exceeding the specified maximum
-   Zero value means no enforcement
-   Sanity check to enforce a minting cap. Should NOT replace proper approval design.

**cosmosCoinBackedPath**

-   IBC backed (sdk.coin) path for the collection
-   Only one path allowed per collection
-   See [IBC Backed Minting Invariants](../../learn/ibc-backed-minting.md) for details

**noForcefulPostMintTransfers**

-   Disallows collection approvals with `overridesFromOutgoingApprovals` or `overridesToIncomingApprovals` set to `true` (excluding the Mint address which is exempt)
-   Prevents forceful transfers that bypass user-level approvals
-   Easy way for you to enforce that there will never be any forceful transfers that bypass user-level approvals ever in your collection.

**disablePoolCreation**

-   Prevents creation of liquidity pools using assets from this collection in our gamm module.
-   When enabled, pool creation attempts will fail

**evmQueryChallenges**

-   Post-transfer EVM query invariants that are checked after all balance updates
-   Each challenge executes a read-only EVM contract query via staticcall
-   If any query returns a result that doesn't match the expected result, the transfer is reverted
-   Useful for enforcing global invariants like maximum holder count, balance caps, or custom compliance rules
-   Placeholders: `$collectionId`, `$sender`, `$initiator`, `$recipient` (first recipient), `$recipients` (all recipients as concatenated 32-byte hex). See [EVM Query Challenges – Placeholders](approval-criteria/evm-query-challenges.md#placeholders) for approval vs invariant placeholder details.
-   See [EVM Query Challenges](approval-criteria/evm-query-challenges.md) for the full challenge structure (fields, operators, gas limits)

```typescript
const evmQueryChallenges: EVMQueryChallenge<bigint>[] = [
    {
        contractAddress: '0xComplianceContract...',
        calldata: '70a08231000000000000000000000000$collectionId',
        expectedResult: '0000000000000000000000000000000000000000000000000000000000000001',
        comparisonOperator: 'eq',
        gasLimit: '100000',
        uri: '',
        customData: '',
    },
];
```

**Important:** Invariants can only be set during collection creation and cannot be updated or removed afterward.

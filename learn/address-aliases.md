# Address Aliases

BitBadges supports string aliases for protocol-derived addresses in transaction messages. Instead of pre-generating opaque `bb1...` addresses, you can use human-readable aliases that the chain resolves automatically.

## Supported Aliases

| Alias | Resolves To | Notes |
| --- | --- | --- |
| `MintEscrow` | The collection's mint escrow `bb1...` address | 1 per collection |
| `CosmosWrapper/0` | First cosmos wrapper path address | Index-based, append-only |
| `CosmosWrapper/1` | Second cosmos wrapper path address | etc. |
| `IBCBacking` | IBC backing path address | 1 per collection |

**Important**: `Mint` is NOT an alias. It is a special non-address string representing unlimited token creation. See [Minting and Circulating Supply](minting-and-circulating-supply.md) for details.

## Where Aliases Work

Aliases can be used in the following message fields:

-   **Transfer `from` and `toAddresses`** fields in `MsgTransferTokens`
-   **Approval `fromListId`, `toListId`, `initiatedByListId`** (embedded in list ID strings)
-   **CoinTransfer `to`** field
-   **MustOwnTokens `ownershipCheckParty`**
-   **DynamicStoreChallenge `ownershipCheckParty`**
-   **UserRoyalties `payoutAddress`**

## How It Works

Aliases are normalized to real `bb1...` addresses at the message handler level before any validation or storage. On-chain storage always contains real addresses, never aliases.

```
User submits tx with "MintEscrow"
        |
        v
Message handler resolves alias to bb1...
        |
        v
Validation and storage use the real bb1... address
```

This means:

-   Queries always return real `bb1...` addresses, not aliases
-   Aliases are a convenience for transaction construction only
-   The resolution is deterministic and collection-specific

## List ID Usage

Aliases can be embedded in list ID patterns, following the same syntax as regular addresses in list IDs (see [Address Lists](../token-standard/learn/address-lists.md)):

-   `"MintEscrow"` -- single-address whitelist containing the mint escrow address
-   `"MintEscrow:bb1other..."` -- multi-address whitelist containing both the mint escrow and another address
-   `"AllWithoutMintEscrow"` -- all addresses except the mint escrow
-   `"!MintEscrow"` -- inversion (blacklist containing only the mint escrow address)

```json
{
    "fromListId": "MintEscrow",
    "toListId": "All",
    "initiatedByListId": "All"
}
```

```json
{
    "fromListId": "AllWithoutMintEscrow:CosmosWrapper/0",
    "toListId": "All",
    "initiatedByListId": "All"
}
```

## Example: Using Aliases in Transfers

```typescript
const transferMsg: MsgTransferTokens = {
    creator: 'bb1user...',
    collectionId: '1',
    transfers: [
        {
            from: 'MintEscrow',
            toAddresses: ['bb1user...'],
            balances: [
                {
                    amount: 10n,
                    tokenIds: [{ start: 1n, end: 10n }],
                    ownershipTimes: [
                        { start: 1n, end: 18446744073709551615n },
                    ],
                },
            ],
        },
    ],
};
```

This is equivalent to looking up the collection's mint escrow `bb1...` address and using it directly, but avoids the need to pre-generate or query it.

## Example: Using Aliases in Approvals

```typescript
const collectionApprovals = [
    {
        fromListId: 'CosmosWrapper/0',
        toListId: 'All',
        initiatedByListId: 'All',
        transferTimes: [{ start: 1n, end: 18446744073709551615n }],
        tokenIds: [{ start: 1n, end: 100n }],
        ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        approvalId: 'unwrap-approval',
        version: 0n,
        approvalCriteria: {
            allowSpecialWrapping: true,
            mustPrioritize: true,
            overridesFromOutgoingApprovals: true,
        },
    },
];
```

## Limitations

-   **Collection-specific**: Aliases resolve to different `bb1...` addresses on different collections. `CosmosWrapper/0` on collection 1 is a different address than `CosmosWrapper/0` on collection 2.
-   **Not available in `MsgCreateAddressLists`**: Stored list IDs cannot use alias-reserved names. Aliases are only resolved in collection-scoped message contexts where the collection ID is known.
-   **Index-based for wrapper paths**: `CosmosWrapper/N` uses append-only indexing. The index corresponds to the order wrapper paths were added to the collection.
-   **SDK/MCP functions still useful**: The `generate_backing_address`, `generate_wrapper_address`, and related SDK functions remain useful for post-creation flows where you need the actual `bb1...` address (e.g., querying balances, off-chain verification).

## Related Pages

-   [Address Lists](../token-standard/learn/address-lists.md) -- list ID syntax and reserved IDs
-   [Minting and Circulating Supply](minting-and-circulating-supply.md) -- the `Mint` special address
-   [Cosmos Coin Wrapper Paths](cosmos-coin-wrapper-paths.md) -- wrapper path addresses that `CosmosWrapper/N` resolves to
-   [IBC Backed Minting](ibc-backed-minting.md) -- IBC backing address that `IBCBacking` resolves to

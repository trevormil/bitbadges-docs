# GetCollectionStats

Retrieves statistics for a collection including holder count and circulating supply.

## Proto Definition

```protobuf
message QueryGetCollectionStatsRequest {
  string collectionId = 1; // Collection ID to query
}

message QueryGetCollectionStatsResponse {
  CollectionStats stats = 1;
}

message CollectionStats {
  string holderCount = 1;      // Number of unique addresses with non-zero balances
  repeated Balance balances = 2; // Circulating supply represented as Balance structures
}

// See all the proto definitions [here](https://github.com/BitBadges/bitbadgeschain/tree/master/proto/tokenization)
```

## Fields

### holderCount

The number of unique addresses that currently hold a non-zero balance in the collection.

**Excluded from count:**
- Special addresses: "Mint", "Total"
- IBC backing path addresses (cosmosCoinBackedPath)
- Cosmos coin wrapper path addresses

**Updated dynamically** as balances change through minting, transfers, backing, and unbacking operations.

### balances

The circulating supply of the collection represented as an array of `Balance` structures. Each balance includes:
- `amount`: The quantity of tokens
- `tokenIds`: Array of token ID ranges
- `ownershipTimes`: Array of ownership time ranges

This tracks the total minted supply minus any tokens that have been backed (locked for IBC operations).

## Usage Example

```bash
# CLI query
bitbadgeschaind query tokenization get-collection-stats [collection-id]

# REST API
curl "https://lcd.bitbadges.io/bitbadges/bitbadgeschain/tokenization/get_collection_stats/1"
```

### Response Example

```json
{
    "stats": {
        "holderCount": "150",
        "balances": [
            {
                "amount": "10000",
                "tokenIds": [{ "start": "1", "end": "100" }],
                "ownershipTimes": [
                    { "start": "1", "end": "18446744073709551615" }
                ]
            }
        ]
    }
}
```

## Use Cases

### Token Analytics
- Track the number of unique holders over time
- Monitor circulating supply for tokenomics

### Compliance
- Verify holder count limits for regulatory compliance
- Track distribution metrics

### Display
- Show holder count on collection pages
- Display circulating supply in UIs

## Related Queries

- [GetCollection](./get-collection.md) - Full collection details
- [GetBalance](./get-balance.md) - Individual user balances

# Fetching Collections

This guide explains how to fetch collection data from the BitBadges API. Collections contain badge metadata, ownership information, and other details.

See the [BitBadges API Documentation](https://bitbadges.stoplight.io/docs/bitbadges) for more details on the API side.
See the [BitBadges SDK Documentation](../../bitbadges-sdk/) for more details on the SDK side.

## Basic Collection Fetching

Here's a simple example of fetching a collection:

```typescript
const res = await BitBadgesApi.getCollections({
    collectionsToFetch: [
        {
            collectionId: 1n,
            metadataToFetch: {
                badgeIds: [{ start: 1n, end: 10n }],
            },
            fetchTotalAndMintBalances: true,
        },
    ],
});
const collection = res.collections[0];

// HTTP equivalent:
// POST /api/v0/collections
// Body: {
//   "collectionsToFetch": [{
//     "collectionId": "1",
//     "metadataToFetch": {
//       "badgeIds": [{"start": "1", "end": "10"}]
//     },
//     "fetchTotalAndMintBalances": true
//   }]
// }
```

## Working with Collection Data

### Fetching Metadata

```typescript
// Get metadata for a specific badge
const badgeMetadata = collection.getBadgeMetadata(1n);

// Get collection-level metadata
const collectionMetadata = collection.getCollectionMetadata();
```

### Checking Balances

```typescript
// Get total supply
const totalSupply = collection.getBadgeBalances('Total');

// Get specific address balance
const userBalance = collection.getBadgeBalances('bb1234...');

// Get full balance info including approvals
const balanceInfo = collection.getBadgeBalanceInfo('bb1234...');
```

## Pagination and Views

Views are used to fetch paginated data like transfer history and owner lists. Each view uses a bookmark system for pagination.

```typescript
// Fetch owners with pagination
const viewRequest = {
    viewType: 'owners',
    viewId: 'owners-list',
    bookmark: '', // Empty for first page
};

// Fetch first page
await collection.fetchNextForView(BitBadgesApi, 'owners', 'owners-list');

// Check if more pages exist
const hasMore = collection.viewHasMore('owners-list');

// Get next bookmark
const nextBookmark = collection.getViewBookmark('owners-list');
```

## Content Warnings

Collections can be flagged as NSFW or reported. These flags will be present in the response:

```typescript
// Example flags in response
{
  nsfw: {
    badgeIds: [{ start: 1n, end: 10n }],
    reason: "Adult content"
  },
  reported: {
    badgeIds: [{ start: 5n, end: 5n }],
    reason: "Copyright violation"
  }
}
```

## Efficient Fetching

To avoid refetching data you already have:

```typescript
// Check if a request would fetch new data
const isRedundant = collection.isRedundantRequest({
    collectionId: 1n,
    metadataToFetch: { badgeIds: [{ start: 1n, end: 10n }] },
});

// Only fetch new badge IDs
await collection.fetchMetadata(BitBadgesApi, {
    metadataToFetch: {
        badgeIds: [{ start: 11n, end: 20n }], // New range
    },
});
```

For complete API documentation, visit the [BitBadges SDK Documentation](../../bitbadges-sdk/).

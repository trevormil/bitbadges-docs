# Managing Views

Throughout the BitBadges API, we use a bookmark-based pagination system for efficient data retrieval. This system is particularly useful when dealing with large datasets that need to be fetched in smaller chunks.

Some endpoints like the get accounts or get collections will use a generic viewsToFetch and views object which can maintain multiple paginated views. Some endpoints will request the bookmark directly. Refer to the documentation for each endpoint to see how it handles pagination.

### How Bookmark Pagination Works

1. **First Request**: For your initial request, use an empty bookmark string (`""`).

```typescript
const firstRequest = {
    viewType: 'owners',
    viewId: 'owners',
    bookmark: '', // Empty for first page
};
```

2. **Response Structure**: Each paginated response includes:
    - The requested data
    - A `bookmark` string for the next page
    - A `hasMore` boolean indicating if more data exists

```typescript
{
    data: [...],
    views: {
        'owners': {
            ids: [...],
            pagination: {
                bookmark: 'abc123...', // Use this for next request
                hasMore: true
            }
        }
    }
}
```

3. **Subsequent Requests**: To fetch the next page, use the bookmark from the previous response:

```typescript
const nextRequest = {
    viewType: 'owners',
    viewId: 'owners',
    bookmark: 'abc123...', // Bookmark from previous response
};
```

4. **Completion**: Continue this process until `hasMore` is `false`.

## Understanding the Views Object

```
Note: This is planning to be deprecated in favor of more confined view routes.
Please use those instead. They are much less confusing.
```

The views object is a central concept in the BitBadges API, used to manage paginated data across different interfaces (BitBadgesCollection, BitBadgesUserInfo, etc). It follows this structure:

```typescript
views: {
    [viewId: string]: {
        ids: string[];          // Array of document IDs
        pagination: {
            bookmark: string;    // Bookmark for next page
            hasMore: boolean;    // Whether more data exists
        };
        type: string;           // The view type
    } | undefined;
}
```

### Key Components

1. **viewId**: A unique identifier for the view
2. **ids**: Array of document IDs that correspond to full documents in the response
3. **pagination**: Contains the bookmark and hasMore flag
4. **type**: Indicates the type of view (e.g., 'owners', 'activity', etc.)

### Document ID Mapping

Documents in the response are referenced by their `_docId` field. To access the full document, you map the IDs from the view to the corresponding array in the response. For example, the activity view documents are stored in the `activity` array, and the view IDs are stored in the `views.activity.ids` array.

```typescript
// Example of accessing activity documents from a view
getActivityView(viewId: string) {
    return (this.views[viewId]?.ids.map((x) => {
        return this.activity.find((y) => y._docId === x);
    }) ?? []);
}
```

### Common View Types

Different interfaces support different view types. See the corresponding documentation for each interface to see what views are supported.

#### Collections Interface

-   `owners`: List of owners
-   `activity`: Transfer activity
-   `approvalTrackers`: Approval tracking documents

See all at [CollectionViewKey](https://bitbadges.github.io/bitbadgesjs/types/CollectionViewKey.html)

#### Account Interface

-   `transferActivity`: User's transfer history
-   `tokensCollected`: Tokens owned by the user
-   `createdTokens`: Collections created by the user
-   `managingTokens`: Collections being managed

See all at [AccountViewKey](https://bitbadges.github.io/bitbadgesjs/types/AccountViewKey.html)

## Helper Functions

The BitBadges SDK provides several helper functions for managing views:

```typescript
// Check if more pages exist
const hasMore = collection.viewHasMore('owners');

// Get bookmark for next page
const nextBookmark = collection.getViewBookmark('owners');

// Fetch next page of data
await collection.fetchNextForView(BitBadgesApi, 'owners', 'owners');

// Get all documents for a view
const ownersView = collection.getOwnersView('owners');
```

## Best Practices

1. **Consistent ViewIds**: Use consistent viewIds when paginating through the same dataset
2. **Error Handling**: Always check for undefined views before accessing
3. **Document Mapping**: Use helper functions when available for mapping IDs to documents
4. **Pagination State**: Track both bookmark and hasMore status for proper pagination
5. **Response Merging**: Remember that each response is confined to its request - use helper functions or manually merge data as needed

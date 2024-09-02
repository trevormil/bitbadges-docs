# Fetching Collections

The main use case of the API are fetching collection and fetching account information. This page explains fetching collections. Collections are stored and fetched as the [BitBadgesCollection ](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs/interfaces/BitBadgesCollection.html)interface. Visit the [SDK docs](../../bitbadges-sdk/) for lots of useful functions for dealing with collections. See the quickstart repo for a working example with fetching them.

```typescript
//Option 1:
const res = await BitBadgesApi.getCollections({
  collectionsToFetch: [
    {
      collectionId: 1n,
      metadataToFetch: {
        badgeIds: [{ start: 1n, end: 10n }],
      },
      fetchTotalAndMintBalances: true,
      viewsToFetch: [
        {
          viewType: 'owners',
          viewId: 'owners',
          bookmark: '',
        },
      ],
    },
  ],
})
const collection = res.collections[0]

//Option 2:
// const collection = await BitBadgesCollection.FetchAndInitialize(BitBadgesApi, { collectionId: 1n, metadataToFetch: { badgeIds: [{ start: 1n, end: 10n }] }, fetchTotalAndMintBalances: true, viewsToFetch: [{ viewType: 'owners', viewId: 'owners', bookmark: '' }] })

console.log(collection.getBadgeMetadata(1n))
console.log(collection.getCollectionMetadata())
console.log(collection.getBadgeBalances('Mint'))
console.log(collection.getBadgeBalances('Total'))
console.log(collection.getBadgeBalanceInfo('cosmos....') //returns whole doc w/ approvals and more not just balances
```

### Pruning Requests + Pruning Paginations

Response details are confined to the request parameters passed in, so this means that merging responses with previous responses needs to be handled. Another thing you might want to do is to not fetch metadata you have previously fetched. For example, if you have already fetched Badge ID 1's metadata, you do not want to fetch it again.

To make this easy, we have exported the following function which prunes requests before they are sent and appends the new results to the cached values.

```typescript
const res2 = await BitBadgesApi.getCollections({
    collectionsToFetch: [
        {
            collectionId: 1n,
            metadataToFetch: { badgeIds: [{ start: 11n, end: 20n }] },
        },
    ],
});
const collection2 = res2.collections[0];
collection.updateWithNewResponse(collection2);
```

Or, if you use many of the class functions, these are handled behind the scenes for you!

```typescript
await collection.fetchMetadata(BitBadgesApi, { metadataToFetch: { badgeIds: [{ start: 11n, end: 20n }] } })
await collection.fetchNextForView(BitBadgesApi, 'owners', 'owners')
await collection.fetchAndUpdate(BitBadgesApi, { .... })
```

You may also find the following functions useful to be more efficient:

```typescript
const isRedundant = collection.isRedundantRequest({...})
const newBody = collection.pruneBody()
```

### Balances

#### Total / Circulating Supply

The "Total" supply balances are treated just as any other user's balances. Use "Total" as the cosmosAddress / address.

**Fetching Balances from API**

To fetch the total and mint balances, you can fetch them in a request with the **fetchMintAndTotalBalances** set to true. They will then be stored in the **owners** array of the returned collection.

To fetch a generic user address balance, you can fetch them with the following command. This will also append that balance to the **owners** array.

```typescript
await collection.fetchBadgeBalances(BitBadgesApi, 'cosmos....');
```

**Using Balances**

You can fetch then use the balances using one of two methods below, but note that they must be fetched prior or else may return undefined (or throw is you use the mustGet function).

```typescript
collection.owners.find((x) => x.cosmosAddress === 'Mint');
collection.owners.find((x) => x.cosmosAddress === 'Total');
```

```typescript
console.log(collection.getBadgeBalances('Mint'))
console.log(collection.getBadgeBalances('Total'))
console.log(collection.getBadgeBalanceInfo('cosmos....') //returns whole doc w/ approvals and more not just balances
```

getBadgeBalances returns just the balances, whereas getBadgeBalanceInfo returns the whole balance document (approvals, permissions, and balances).

**Fetching Speciifc Badge Balances**

Everything above handles balances on a collection level, but sometimes, you may want to fetch activity / balances for a specific badge ID. You can do so via the badge-specific API routes; however, note you have to handle paginations yourself.

```
const { activity, pagination } = await collection.getBadgeActivity(BitBadgesApi, 1n, { bookmark: '...' })
const { owners, pagination } = await collection.getOwnersForBadge(BitBadgesApi, 1n, { bookmark: '...' })
```

### NSFW / Reported

NSFW or reported collections will be flagged with the **nsfw** or **reported** properties. Everything is still fetched as normal with typical requests, and it is up to you to do what you want with these accounts.

On the app, we provide a warning message and replace all metadata with placeholders.

```typescript
nsfw?: { badgeIds: UintRange<T>[], reason: string };
reported?: { badgeIds: UintRange<T>[], reason: string };
```

### Metadata

The fetched collection metadata (if requested) will be stored in **cachedCollectionMetadata.** The requested badge metadata will be fetched and stored in **cachedBadgeMetadata.** Subsequent fetches will append the new metadata to the existing **cachedBadgeMetadata**. See the SDK for handling these fields.

{% content-ref url="../../bitbadges-sdk/common-snippets/badge-metadata.md" %}
[badge-metadata.md](../../bitbadges-sdk/common-snippets/badge-metadata.md)
{% endcontent-ref %}

Note this is different from **collectionMetadataTimeline** and **badgeMetadataTimeline** which simply store the URIs, not the actual metadata itself.

```typescript
cachedCollectionMetadata?: Metadata<T>;
cachedBadgeMetadata: BadgeMetadataDetails<T>[];
collectionMetadataTimeline: CollectionMetadataTimeline<T>[];
badgeMetadataTimeline: BadgeMetadataTimeline<T>[];
```

<pre class="language-typescript"><code class="lang-typescript">export interface TimelineItem&#x3C;T extends NumberType> {
  timelineTimes: UintRange&#x3C;T>[];
}

export interface CollectionMetadata {
  uri: string
  customData: string
}

export interface BadgeMetadata&#x3C;T extends NumberType> {
  uri: string
  customData: string
  badgeIds: UintRange&#x3C;T>[]
}

export interface BadgeMetadataTimeline&#x3C;T extends NumberType> extends TimelineItem&#x3C;T> {
  badgeMetadata: BadgeMetadata&#x3C;T>[]
}

<strong>export interface CollectionMetadataTimeline&#x3C;T extends NumberType> extends TimelineItem&#x3C;T> {
</strong>  collectionMetadata: CollectionMetadata
}
</code></pre>

```typescript
export interface BadgeMetadataDetails<T extends NumberType> {
    metadataId?: T;
    badgeIds: UintRange<T>[];
    metadata: Metadata<T>;
    uri?: string;
    customData?: string;

    toUpdate?: boolean;
}
```

```json
cachedBadgeMetdata: [{
    "metadata": {
        "name": "Verification",
        "description": "The Verification Badge is a symbol of trust and authenticity in the digital world. It's the ultimate proof that the holder's identity or credentials have been verified, making it invaluable for influencers, celebrities, and professionals. With this badge, users can gain credibility, attract more followers, and ensure that their online presence is recognized as genuine and reliable.",
        "image": "ipfs://QmPfdaLWBUxH6ZrWmX1t7zf6zDiNdyZomafBqY5V5Lgwvj",
        "fetchedAt": "1704837218398",
        "fetchedAtBlock": "24",
        "_isUpdating": false
    },
    "badgeIds": [
        {
            "start": "1",
            "end": "1"
        }
    ],
    "uri": "ipfs://QmTpELFKNwxTEHPSb8Jkkbyt76syfGMdnM8XvQtizZaz8Q",
    "metadataId": "1"
}, ...
],
cachedCollectionMetadata: {
    "name": "Verification",
    "description": "The Verification Badge is a symbol of trust and authenticity in the digital world. It's the ultimate proof that the holder's identity or credentials have been verified, making it invaluable for influencers, celebrities, and professionals. With this badge, users can gain credibility, attract more followers, and ensure that their online presence is recognized as genuine and reliable.",
    "image": "ipfs://QmPfdaLWBUxH6ZrWmX1t7zf6zDiNdyZomafBqY5V5Lgwvj",
    "fetchedAt": "1704837218398",
    "fetchedAtBlock": "24",
    "_isUpdating": false
}
```

### **Views / Paginations**

The collection interface uses a view system with paginations + bookmarks.

Views have a base **viewType** describing the query type and a unique **viewId** for identification. Each request you will pass in a bookmark obtained from the previous request (pass in '' for the first request). This will fetch the next 25 documents for that view. Once no more docs can be fetched, the returned **hasMore** will be false.

The collection interface supports the following base **viewType** values.

* 'transferActivity' : Fetches latest transfer activity documents.
* 'amountTrackers': Fetches latest amount trackers for collection
* 'challengeTrackers': Fetches latest challenge trackers for collection
* 'owners': Fetches owners of the collection

```typescript
export type CollectionViewKey =
    | 'transferActivity'
    | 'owners'
    | 'amountTrackers'
    | 'challengeTrackers';
```

```
const hasMore = collection.viewHasMore('owners')
const pagination = collection.getViewPagination('owners')
const bookmark = collection.getViewBookmark('owners')

await collection.fetchNextForView(BitBadgesApi, 'owners', 'owners');

const ownersView = collection.getOwnersView('owners')
```

The collection interface also supports different filtering options. Make sure that all fetches with the same viewId specify the same filter options.

```ts
viewsToFetch?: {
  //The base view type to fetch.
  viewType: CollectionViewKey;
  //A unique view ID. This is used for pagination. All fetches w/ same ID should be made with same criteria.
  viewId: string;
  //A bookmark to pass in for pagination. "" for first request.
  bookmark: string;
  //If defined, we will return the oldest items first..
  oldestFirst?: boolean;
}[];
```

Request:

<pre class="language-json"><code class="lang-json"><strong>viewsToFetch: [{
</strong>    viewType: 'owners',
    viewId: 'uniqueOwnersId',
    bookmark: ''
}]
</code></pre>

Response:

```json
{
    ...,
    owners: [....],
    views: {
        uniqueOwnersId: {
            ids: [...],
            pagination: {
                bookmark: 'abc',
                hasMore: true
            }
        }
    }
}
```

Next Request:

<pre class="language-json"><code class="lang-json"><strong>viewsToFetch: [{
</strong>    viewType: 'owners',
    viewId: 'uniqueOwnersId',
    bookmark: 'abc'
}]
</code></pre>

And so on. Remember, each response is confined to its request, so it will fetch the next 25 docs, but you have to append it to the previous 25 docs as explained above.

The **ids** returned in each view will correspond to the **\_docId** field in its corresponding array (e.g. collection.activity for the 'latestActivity' view).

This logic is handled behind the scenes with the corresponding helper function

```typescript
const collectedView = collection.getOwnersView('owners');
```

### **Fetch Route**

#### **POST /api/v0/collections - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs/interfaces/GetCollectionsPayload.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs/interfaces/GetCollectionBatchSuccessResponse.html)**)**

Batch fetch details about multiple collections.

```typescript
export interface GetCollectionsPayload {
    collectionsToFetch: ({
        collectionId: NumberType;
    } & GetMetadataForCollectionPayload &
        GetAdditionalCollectionDetailsPayload)[];
}

export interface GetAdditionalCollectionDetailsPayload {
    /**
     * If present, the specified views will be fetched.
     */
    viewsToFetch?: {
        viewType: CollectionViewKey;
        viewId: string;
        bookmark: string;
    }[];

    /**
     * If true, the total and mint balances will be fetched and will be put in owners[].
     */
    fetchTotalAndMintBalances?: boolean;
    /**
     * If present, the merkle challenges corresponding to the specified merkle challenge IDs will be fetched.
     */
    challengeTrackersToFetch?: ChallengeTrackerIdDetails<NumberType>[];
    /**
     * If present, the approvals trackers corresponding to the specified approvals tracker IDs will be fetched.
     */
    approvalTrackersToFetch?: AmountTrackerIdDetails<NumberType>[];
    /**
     * If true, we will append defaults (such as timelines) with empty values where applicable.
     */
    handleAllAndAppendDefaults?: boolean;
}

export interface GetMetadataForCollectionPayload {
    /**
     * If present, we will fetch the metadata corresponding to the specified options.
     */
    metadataToFetch?: MetadataFetchOptions;
}

export interface MetadataFetchOptions {
    /**
     * If true, collection metadata will not be fetched.
     */
    doNotFetchCollectionMetadata?: boolean;
    /**
     * If present, the metadata corresponding to the specified metadata IDs will be fetched. See documentation for how to determine metadata IDs.
     */
    metadataIds?: NumberType[] | UintRange<NumberType>[];
    /**
     * If present, the metadata corresponding to the specified URIs will be fetched.
     */
    uris?: string[];
    /**
     * If present, the metadata corresponding to the specified badge IDs will be fetched.
     */
    badgeIds?: NumberType[] | UintRange<NumberType>[];
}
```

```typescript
export interface GetCollectionBatchSuccessResponse<T extends NumberType> {
    collections: BitBadgesCollection<T>[];
}
```

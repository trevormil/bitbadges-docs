# Fetching Collections

The main use case of the API are fetching collection and fetching account information. This page explains fetching collections. Collections are stored and fetched as the [BitBadgesCollection ](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/BitBadgesCollection.html)interface.

<pre class="language-typescript"><code class="lang-typescript"><strong>await BitBadgesApi.getCollections([{
</strong><strong>    //example
</strong><strong>    collectionId: 1n,
</strong><strong>    metadataToFetch: {
</strong><strong>        badgeIds: [{ start: 1n, end: 10n }]
</strong><strong>    },
</strong><strong>    fetchTotalAndMintBalances: true,
</strong><strong>    viewsToFetch: [{
</strong>        viewType: 'owners',
        viewId: 'owners',
        bookmark: ''
    }]
<strong>}])
</strong></code></pre>

### **Confined Responses**

Response details are confined to the request parameters passed in, so you will have to handle merging the collection response with previous responses. We have made it easy to do with the SDK. This handles everything from new bookmarks and pagination to appending newly fetched metadata.

```typescript
import { updateCollectionWithResponse } from "bitbadgesjs-utils"

export function updateCollectionWithResponse(
    oldCollection: BitBadgesCollection<bigint> | undefined, 
    newCollectionResponse: BitBadgesCollection<bigint>
): BitBadgesCollection<bigint>
```

### **Pruning Metadata**

Another thing you might want to do is to not fetch metadata you have previously fetched. For example, if you have already fetched Badge ID 1's metadata, you do not want to fetch it again.

This can be handled simply by using the SDK's pruneMetadataToFetch function. This will scan your existing cached collection, see what you have already fetched, and prune your request to only unfetched metadata. However, this is not an excuse to be lazy and fetch too much at one time. There is still a 250 URI limit put in place per fetch.

```typescript
import { pruneMetadataToFetch } from "bitbadgesjs-utils"

export function pruneMetadataToFetch: (
    cachedCollection: BitBadgesCollection<bigint>, 
    metadataFetchReq?: MetadataFetchOptions
) => {
    doNotFetchCollectionMetadata: boolean;
    uris: string[];
} //compatible with MetadataFetchOptions interface
```

### **Putting It Together**

<pre class="language-typescript"><code class="lang-typescript"><strong>const prunedMetadataToFetch = pruneMetadataToFetch(
</strong><strong>    collection, 
</strong><strong>    requestBody.metadataToFetch
</strong><strong>)
</strong><strong>const collections = await BitBadgesApi.getCollections([
</strong><strong>    {
</strong><strong>        ...requestBody,
</strong><strong>        metadataToFetch: prunedMetadataToFetch
</strong><strong>    }
</strong><strong>])
</strong><strong>const newCollection = collections[0];
</strong><strong>const updatedCollection = updateCollectionWithResponse(collection, newCollection);
</strong></code></pre>

### NSFW / Reported

NSFW or reported collections will be flagged with the **nsfw** or **reported** properties.
Everything is still fetched as normal with typical requests, and it is up to you to do what you 
want with these accounts. 

On the app, we provide a warning message and replace all metadata with placeholders.

### Metadata

The fetched collection metadata (if requested) will be stored in **cachedCollectionMetadata.** The requested badge metadata will be fetched and stored in **cachedBadgeMetadata.**

Note this is different from **collectionMetadataTimeline** and **badgeMetadataTimeline** which simply store the URIs, not the actual metadata itself.

### **Views / Paginations**

The collection interface uses a view system with paginations + bookmarks.

Views have a base **viewType** describing the query type and a unique **viewId** for identification. Each request you will pass in a bookmark obtained from the previous request (pass in '' for the first request). This will fetch the next 25 documents for that view. Once no more docs can be fetched, the returned **hasMore** will be false.&#x20;

The collection interface supports the following base **viewType** values.&#x20;

* 'latestActivity' : Fetches latest transfer activity documents.&#x20;
* 'latestReviews': Fetches latest reviews for collection
* 'owners': Fetches owners of the collection
* 'merkleChallenges': Fetches merkle challenge trackers for the collection
* 'approvalsTrackers': Fetches approval trackers for the collection

```typescript
export type CollectionViewKey = 'latestActivity' | 'latestReviews' | 'owners' | 'merkleChallenges' | 'approvalsTrackers';
```

Request:

<pre class="language-json"><code class="lang-json"><strong>viewsToFetch: [{
</strong>    viewType: 'owners',
    viewId: 'uniqueOwnersId',
    bookmark: ''
}]
</code></pre>

Response:&#x20;

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

The **ids** returned in each view will correspond to the **\_legacyId** field in its corresponding array (e.g. collection.activity for the 'latestActivity' view).

```typescript
export function getCollectionActivityView(collection: BitBadgesCollection<bigint>, viewType: CollectionViewKey) {
  return (collection.views[viewType]?.ids.map(x => {
    return collection.activity.find(y => y._legacyId === x);
  }) ?? []) as TransferActivityDoc<bigint>[]
}

export function getCollectionReviewsView(collection: BitBadgesCollection<bigint>, viewType: CollectionViewKey) {
  return (collection.views[viewType]?.ids.map(x => {
    return collection.reviews.find(y => y._legacyId === x);
  }) ?? []) as ReviewDoc<bigint>[];
}

export function getCollectionBalancesView(collection: BitBadgesCollection<bigint>, viewType: CollectionViewKey) {
  return (collection.views[viewType]?.ids.map(x => {
    return collection.owners.find(y => y._legacyId === x);
  }) ?? []) as BalanceDoc<bigint>[]
}

export function getCollectionMerkleChallengeTrackersView(collection: BitBadgesCollection<bigint>, viewType: CollectionViewKey) {
  return (collection.views[viewType]?.ids.map(x => {
    return collection.merkleChallenges.find(y => y._legacyId === x);
  }) ?? []) as MerkleChallengeDoc<bigint>[]
}

export function getCollectionApprovalTrackersView(collection: BitBadgesCollection<bigint>, viewType: CollectionViewKey) {
  return (collection.views[viewType]?.ids.map(x => {
    return collection.approvalsTrackers.find(y => y._legacyId === x);
  }) ?? []) as ApprovalsTrackerDoc<bigint>[]
}
```

### Mint / Total Supply Balances

The "Mint" or unminted balances as well as the "Total" supply balances are treated just as any other user's balances. To fetch them, you can fetch them in a request with the **fetchMintAndTotalBalances** set to true. They will then be stored in the **owners** array of the returned collection.

```typescript
collection.owners.find(x => x.cosmosAddress === "Mint")
collection.owners.find(x => x.cosmosAddress === "Total")
```

### **Fetch Route**&#x20;

#### **POST /api/v0/collection/batch - (**[**Request**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionBatchRouteRequestBody.html)**,** [**Response**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/GetCollectionBatchRouteSuccessResponse.html)**)**

Batch fetch details about multiple collections.&#x20;

```typescript
export interface GetCollectionBatchRouteRequestBody {
  collectionsToFetch: ({ collectionId: NumberType } 
    & GetMetadataForCollectionRequestBody 
    & GetAdditionalCollectionDetailsRequestBody
  )[],
}

export interface GetAdditionalCollectionDetailsRequestBody {
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
  merkleChallengeIdsToFetch?: ChallengeTrackerIdDetails<NumberType>[];
  /**
   * If present, the approvals trackers corresponding to the specified approvals tracker IDs will be fetched.
   */
  approvalsTrackerIdsToFetch?: AmountTrackerIdDetails<NumberType>[];
  /**
   * If true, we will append defaults (such as timelines) with empty values where applicable.
   */
  handleAllAndAppendDefaults?: boolean;
}

export interface GetMetadataForCollectionRequestBody {
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
export interface GetCollectionBatchRouteSuccessResponse<T extends NumberType> {
  collections: BitBadgesCollection<T>[]
}
```


### **Interfaces**

```ts
export interface BitBadgesCollection<T extends NumberType> extends CollectionInfoBase<T> {
  collectionApprovals: CollectionApprovalWithDetails<T>[];
  collectionPermissions: CollectionPermissionsWithDetails<T>;
  defaultBalances: UserBalanceStoreWithDetails<T>;

  //The following are to be fetched dynamically and as needed from the DB
  cachedCollectionMetadata?: Metadata<T>;
  cachedBadgeMetadata: BadgeMetadataDetails<T>[];
  activity: TransferActivityDoc<T>[],
  announcements: AnnouncementDoc<T>[],
  reviews: ReviewDoc<T>[],
  owners: BalanceDocWithDetails<T>[],
  merkleChallenges: MerkleChallengeDoc<T>[],
  approvalsTrackers: ApprovalsTrackerDoc<T>[],

  nsfw?: { badgeIds: UintRange<T>[], reason: string };
  reported?: { badgeIds: UintRange<T>[], reason: string };

  views: {
    [viewId: string]: {
      ids: string[],
      type: string,
      pagination: PaginationInfo,
    } | undefined
  }
}

export interface CollectionInfoBase<T extends NumberType> {
  collectionId: T;
  collectionMetadataTimeline: CollectionMetadataTimeline<T>[];
  badgeMetadataTimeline: BadgeMetadataTimeline<T>[];
  balancesType: "Standard" | "Off-Chain - Indexed" | "Inherited" | "Off-Chain - Non-Indexed";
  offChainBalancesMetadataTimeline: OffChainBalancesMetadataTimeline<T>[];
  customDataTimeline: CustomDataTimeline<T>[];
  managerTimeline: ManagerTimeline<T>[];
  collectionPermissions: CollectionPermissions<T>;
  collectionApprovals: CollectionApproval<T>[];
  standardsTimeline: StandardsTimeline<T>[];
  isArchivedTimeline: IsArchivedTimeline<T>[];
  defaultBalances: UserBalanceStore<T>;
  createdBy: string;
  createdBlock: T;
  createdTimestamp: T;
  updateHistory: {
    txHash: string;
    block: T;
    blockTimestamp: T;
  }[];
  aliasAddress: string;
}
```
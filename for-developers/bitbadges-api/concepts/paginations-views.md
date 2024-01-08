# Paginations / Views

**Paginations**

Throughout the API, we use a bookmark technique.

For the first request, you will not need to specify a bookmark, and it will fetch the first page. Within the response, it will return a **bookmark** and **hasMore**. **hasMore** defines whether there are more pages to be fetched. To fetch the next page, you will specify the returned bookmark from the previous request to the next request. This process can be repeated until all are loaded.&#x20;

This is all handled via the [PaginationInfo](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/PaginationInfo.html) type.

**Views**&#x20;

We support a few views that can be fetched for accounts and collections.&#x20;

```typescript
export type CollectionViewKey = 'latestActivity' | 'latestAnnouncements' | 'latestReviews' | 'owners' | 'merkleChallenges' | 'approvalsTrackers';
```

**Example**

We request the "badgesCollected" view with no bookmark (first page). We then receive the bookmark "1". Next request, we will pass in bookmark "1" for feching the next entries. However, **hasMore**is false here, so we know it is doesn't have anymore entries.

Note that the views only stores the IDs (in this case, \["2:cosmos..."]). Each ID will correspond to a doc returned in another field (in this case, the "collected" field). See the second picture below. We do this because multiple views can correspond to the same documents (e.g. "badgesCollected" and "badgesCollectedWithHidden"), so we don't doubly return each document if unnecessary.



<figure><img src="../../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

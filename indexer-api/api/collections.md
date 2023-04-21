# Collections

With collections, we store the core collection details (see [StoredBadgeCollection](types.md)) in one document. In other documents, we store the metadata, activity, announcements, etc for a collection.

**Metadata**

For metadata, we store documents according to batch IDs which start at 0. ID 0 will be the collection metadata. Then, we iterate linearly through the collection's [badgeUris](types.md) and store the metadata of each badge. ID 1 will be the first URI fetched. ID 2 will be the next and so on.&#x20;

When we fetch metadata, we only allow fetching 100 at a time. To get another 100, call the **POST /api/v0/collection/:id/metadata** route with the corresponding startBatchId.

Use **TODO** to get the batch ID for a specific badge. and TODO to update a specific metadata map

**Announcements and Activity**

For announcements and activity, we use a bookmark technique. For the first request, you will not need to specify a bookmark, and it will fetch the X most recent announcements or activity items. If there are > X documents, you can fetch the next X by specifying the bookmark returned from the previous call. This process can be repeated until all are loaded. When there are no more documents left, the hasMore field returned will be false.



### POST /api/v0/collection/batch

Fetches the collection details, including 100 metadata documents for the given collection IDs starting at each specified startBatchId. See explanation above for metadata batch IDs.

#### Request

* Body:
*   <pre class="language-json"><code class="lang-json"><strong>{
    </strong>  "collections": [1, 2, 3],
      "startBatchIds": [0, 0, 100]
    }
    </code></pre>

    The `collections` field is an array of numbers representing the collection IDs to fetch. The `startBatchIds` field is an array of numbers representing the metadata batch IDs to start fetching documents from.

#### Response

* Status Code: 200
* Body:
*   ```json
    {
      "collections": [
        {
          "collectionId": 1,
          "collectionUri": "https://example.com/collection/1",
          "badgeUris": ...,
          ...
        },
        ...
      ]
    }
    ```

    Returns the collection details. See [StoredBadgeCollection](types.md) . Note that collectionMetadata and badgeMetadata will only include the 100 metadata documents fetched, meaning certain metadata may not be present. To fetch additional metadata, call this route again or the getMetadata route with an updated startBatchId. Update your existing metadata accordingly.

### POST /api/v0/collection/query

Queries the collections, for those that match a [CouchDB mango query](https://docs.couchdb.org/en/stable/api/database/find.html) specified.

#### Request

* Body:
* <pre class="language-json"><code class="lang-json"><strong>{
  </strong>  "selector": {
      ... (CouchDB Mango Query Selector)
    }
  }
  </code></pre>

#### Response

* Status Code: 200
* Body:
*   ```json
    {
      "collections": [
        {
          "collectionId": 1,
          "collectionUri": "https://example.com/collection/1",
          "badgeUris": ...,
          ...
        },
        ...
      ]
    }
    ```

    Returns the collection details. See [StoredBadgeCollection](types.md) . No metadata will be fetched.

### POST /api/v0/collection/:id

Fetches the collection details for the specified ID (:id). Gets first 100 metadata documents as well.

#### Request

* Body:
* <pre class="language-json"><code class="lang-json"><strong>{
  </strong>  "activityBookmark": "...",
    "announcementsBookmark": "..."
  }
  </code></pre>

#### Response

* Status Code: 200
* Body:
*   ```json
    {
        pagination: {
            activity: {
                bookmark: "...",
                hasMore: boolean
            },
            announcements: {
                bookmark: "...",
                hasMore: boolean
            }
        },
        collection: {
            ... (Collection Details)
        }
    }
    ```

    Returns the collection details. See [StoredBadgeCollection](types.md). See explanation at top of page for annoucnements / activity bookmarks.

### POST /api/v0/collection/:id/metadata

Fetches 100 metadata documents for a collection (:id) starting at the startBatchId (default: 0).

#### Request

* Body:
* <pre class="language-json"><code class="lang-json"><strong>{
  </strong>  "startBatchId": number,
  }
  </code></pre>

#### Response

* Status Code: 200
* Body:
*   ```json
    {
        "collectionMetadata": { ... },
        "badgeMetadata": BadgeMetadataMap,
    }
    ```

    Returns the metadata for a collection. Note that it only fetches 100 documents, starting at startBatchId, and thus, certain metadata for the collection and/or badges may be missing. Update your metadata accordingly by calling this route with different startBatchIds. See explanations at top of page.

### POST /api/v0/collection/:id/:badgeId/activity

Gets activity for a specific badge in a collection. For fetching activity for the whole collection (i.e. all badge IDs), use the collection routes.

#### Request

* Body:
* <pre class="language-json"><code class="lang-json"><strong>{
  </strong>  "bookmark": "..."
  }
  </code></pre>

#### Response

* Status Code: 200
* Body:
* ```json
  {
      pagination: {
          activity: {
              bookmark: "...",
              hasMore: boolean
          }
      },
      activity: ActivityItem[]
  }
  ```

Returns the activity for a specific badge ID.

### POST /api/v0/collection/:id//refreshMetadata

### POST /api/v0/collection/:id/:badgeId/refreshMetadata

Refreshes metadata for a collection (:id) and / or a specific badge ID (:badgeId). By refresh, we attempt to fetch the metadata again from the provided URI.

There are two routes here. If the one with no :badgeId is used, we assume the whole collection is to be refreshed. You can optionally only refresh the collection metadata by using the route with no :badgeId and specifying the onlyCollectionMetadata option.



Note metadata refreshes are added to a queue and may not execute it immediately. It may take awhile to update, depending on the business of the queue.

**Manager-Only Authenticated Route**

Must be signed in and be the manager of the collection.

#### Request

* Body:
* <pre class="language-json"><code class="lang-json"><strong>{
  </strong>  "onlyCollectionMetadata": boolean
  }
  </code></pre>

#### Response

* Status Code: 200
* Body:
* ```json
  {
      message: "Added to queue!"
  }
  ```






# Metadata IDs

Metadata IDs are a way of representing the metadata corresponding to badges by an ID rather than using the badge ID. Each ID represents one URI fetch.&#x20;

**What are metadata IDs?**

While we do support fetching badge metadata from the API via Badge IDs for ease of use, we recommend fetching via metadata IDs because this directly corresponds to the number of fetches required (aka unique URIs).

For example, a collection of 10000 badges may have 10000 unique metadata URIs to fetch or just one if all badges share the same metadata (excluding the collection metadata).

Metadata IDs are simply an ID number that corresponds to a deterministic calculation of what URIs to fetch.&#x20;

**How do we compute them?**

ID 0 corresponds to the collection metadata URI. Then, we linearly scan through the badge metadata values ([BadgeMetadata](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/BadgeMetadata.html)\[]) and each unique URI increments the metadata ID by 1.&#x20;

If a badge metadata URI is stored with "{id}" (indicating to be replaced by the badge ID), we consider each URI unique because it will be unique after replacement.

We use the following logic to deterministically compute, fetch, and store them in the indexer:

```
currMetadataId = 1
for each badgeMetadataObj in badgeMetadata: //Linear iteration
  if badgeMetadataObj.uri.contains("{id}"):
    for each id in badgeMetadataObj.badgeIds:
      fetch metadata from badgeUri.uri.replace("{id}", id)
      store metadata in database with metadataId = currMetadataId and badgeIds = [{start: id, end: id}]
  else:
   fetch metadata from badgeUri.uri
   store metadata in database with metadataId = currMetadataId and badgeIds = badgeUri.badgeIds
  currMetadataId++
```





Use the following exported functions from bitbadgesjs-utils to convert between:

```typescript
function getBadgeIdsForMetadataId(metadataId: bigint, badgeUris: BadgeMetadata<bigint>[])
```

```typescript
function getUrisForMetadataIds(metadataIds: bigint[], collectionUri: string, badgeUris: BadgeMetadata<bigint>[])
```

```typescript
const getMetadataIdForUri = (uri: string, badgeUris: BadgeMetadata<bigint>[])
```

```typescript
const getMetadataIdForBadgeId = (badgeId: bigint, badgeUris: BadgeMetadata<bigint>[])
```

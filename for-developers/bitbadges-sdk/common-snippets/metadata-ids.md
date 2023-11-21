# Metadata IDs

Metadata IDs are a way of representing the metadata corresponding to badges by an ID rather than using the badge ID. Each ID represents one URI fetch. See [Metadata IDs](../../bitbadges-api/concepts/badge-metadata.md) for more info.



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

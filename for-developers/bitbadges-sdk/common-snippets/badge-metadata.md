# Badge Metadata

#### Tutorial: Managing Badge Metadata in TypeScript

**1. Introduction to `BadgeMetadataDetails`**

The `BadgeMetadataDetails` type captures comprehensive details about the metadata of a badge. It contains fields such as badge IDs (ranges), associated metadata, a URI, and custom data. This is what is used via the **cachedBadgeMetadata** field from collection responses.

**2. Removing Metadata for Specific Badge IDs**

To delete metadata associated with specific badge IDs:

```typescript
const currentMetadata: BadgeMetadataDetails<bigint>[] = [...]; // your current metadata array

const badgeIdsToRemove = UintRangeArray.From([
  { start: 5n, end: 10n }
];

const updatedMetadata = removeBadgeMetadata(currentMetadata, badgeIdsToRemove);
console.log(updatedMetadata); // This will show metadata without the removed badge IDs.
```

**3. Updating Metadata for Badges**

If you wish to update specific badge metadata in the badge metadata details:

```typescript
const currentMetadata: BadgeMetadataDetails<bigint>[] = [...]; // your current metadata array

const metadataToUpdate: BadgeMetadataDetails<bigint> = new BadgeMetadataDetails<bigint>({
  badgeIds: [{ start: 7n, end: 7n }],
  metadata: { /* your metadata details here */ },
  uri: "http://new-metadata-url.com", //Or 'Placeholder' or something else
  customData: "Some custom information",
});

const newMetadataArray = updateBadgeMetadata(currentMetadata, metadataToUpdate);
console.log(newMetadataArray); // This will show the array with the updated metadata.
```

**4. Fetch Metadata Details for a Specific Badge ID**

To retrieve metadata details for a particular badge ID:

```typescript
const badgeIdToFetch = 12n;

const badgeMetadataDetails = getMetadataDetailsForBadgeId(badgeIdToFetch, currentMetadata);
console.log(badgeMetadataDetails); // This will display the metadata details for the specified badge ID.
```

**5. Fetch Only the Metadata for a Specific Badge ID**

To only retrieve the metadata (without the surrounding details) for a particular badge ID:

```typescript
const badgeIdToFetch = 15n;

const badgeMetadata = getMetadataForBadgeId(badgeIdToFetch, currentMetadata);
console.log(badgeMetadata); // This will show only the metadata for the given badge ID.
```

**Conclusion**

These functions provide a robust toolkit for managing badge metadata. Whether you're updating, fetching, or deleting metadata associated with badges, you have a systematic and structured approach available.

# Timelines

BitBadges uses timeline-based fields to allow dynamic, time-dependent values for various attributes. This feature enables automatic updates to field values based on the current time, without requiring additional blockchain transactions.

### Key Concepts

1. **Time Representation**:
   * Times are represented as UNIX time (milliseconds since the epoch).
   * Epoch: Midnight at the beginning of January 1, 1970, UTC.
2. **Value Assignment**:
   * Values are assigned to specific time ranges.
   * No overlapping time ranges are allowed for a single field.
3. **Default Behavior**:
   * If no value is set for the current time, the field assumes an empty/null/default value.

### Structure

Timeline-based fields extend the `TimelineItem` interface:

```typescript
export interface TimelineItem<T extends NumberType> {
  timelineTimes: UintRange<T>[];
}
```

### Example Timeline Fields

The collection interface includes the following timeline-based fields:

* `managerTimeline: ManagerTimeline<T>[]`
* `collectionMetadataTimeline: CollectionMetadataTimeline<T>[]`
* `badgeMetadataTimeline: BadgeMetadataTimeline<T>[]`
* `offChainBalancesMetadataTimeline: OffChainBalancesMetadataTimeline<T>[]`
* `customDataTimeline: CustomDataTimeline<T>[]`
* `standardsTimeline: StandardsTimeline<T>[]`
* `isArchivedTimeline: IsArchivedTimeline<T>[]`

### Example: CollectionMetadataTimeline

#### Protocol Buffers Definition

```protobuf
message CollectionMetadataTimeline {
  CollectionMetadata collectionMetadata = 1;
  repeated UintRange timelineTimes = 2;
}
```

#### Usage Example

```typescript
const collectionMetadataTimeline = [
  {
    timelineTimes: [{ start: 1n, end: 10n }],
    collectionMetadata: {
      uri: 'ipfs://abc123',
      customData: '',
    },
  },
  {
    timelineTimes: [{ start: 11n, end: 10000000n }],
    collectionMetadata: {
      uri: 'ipfs://xyz456',
      customData: '',
    },
  }
];
```

In this example:

* From time 1 to 10, the collection metadata URI is 'ipfs://abc123'.
* From time 11 to 10000000, the collection metadata URI is 'ipfs://xyz456'.
* At time 5, the first entry would be used.
* At time 20, the second entry would be used.

### Practical Application

This feature allows for automatic, time-based updates to collection attributes. For example, you can set a collection's metadata URL to change at specific times:

```typescript
const metadataTimeline = [
  {
    timelineTimes: [{ start: 1672531200000n, end: 1680307199000n }], // Jan 1, 2023 to Mar 31, 2023
    collectionMetadata: { uri: 'https://example1.com', customData: '' },
  },
  {
    timelineTimes: [{ start: 1680307200000n, end: 1704067199000n }], // Apr 1, 2023 to Dec 31, 2023
    collectionMetadata: { uri: 'https://example2.com', customData: '' },
  }
];
```

This setup would automatically switch the metadata URL from example1.com to example2.com on April 1, 2023, without requiring any additional blockchain transactions.

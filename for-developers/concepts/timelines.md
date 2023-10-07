# ⏳ Timelines

Certain fields in the BitBadges interface are timeline-based fields. This means that you can set when a field is a certain value based on the current time. Whenever it is queried, it is expected that the querier uses the value set for the current time.

Setting a field to have multiple values at the same time is disallowed (i.e. no duplicate timeline times). If the current time does not have a corresponding value set, we assume the value is empty / null.

Times are defined via [UNIX time](https://developer.mozilla.org/en-US/docs/Glossary/Unix\_time) or the number of milliseconds elapsed since the [epoch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Date#the\_epoch\_timestamps\_and\_invalid\_date), which is defined as the midnight at the beginning of January 1, 1970, UTC.

For example, one can set the collection metadata URL to be https://example1.com from January to March and have it automatically switch to https://example2.com for March to December. The switch happens automatically and doesn't need any blockchain transaction to trigger it at that time.

```typescript
managerTimeline: ManagerTimeline<T>[],
collectionMetadataTimeline: CollectionMetadataTimeline<T>[],
badgeMetadataTimeline: BadgeMetadataTimeline<T>[],
offChainBalancesMetadataTimeline: OffChainBalancesMetadataTimeline<T>[],
customDataTimeline: CustomDataTimeline<T>[],
collectionApprovals: CollectionApproval<T>[],
standardsTimeline: StandardsTimeline<T>[],
contractAddressTimeline: ContractAddressTimeline<T>[],
isArchivedTimeline: IsArchivedTimeline<T>[],
```

These all extend the following interface&#x20;

```typescript
export interface TimelineItem<T extends NumberType> {
  timelineTimes: UintRange<T>[];
}
```

**Examples**

```protobuf
message CollectionMetadataTimeline {
  CollectionMetadata collectionMetadata = 1;
  repeated UintRange timelineTimes = 2;
}
```

Below would denote collection metadata which has URI abc123 from time 1 to time 10 and URI xyz456 from 11-1000000.

```typescript
[{
  timelineTimes: [{ start: 1n, end: 10n }],
  collectionMetadata: {
    uri: 'ipfs://abc123',
    customData: '',
  },
}, {
  timelineTimes: [{ start: 11n, end: 10000000n }],
  collectionMetadata: {
    uri: 'ipfs://xyz456',
    customData: '',
  },
}];
```

If it is time 5, we use the first. If it is time 20, we use the second.

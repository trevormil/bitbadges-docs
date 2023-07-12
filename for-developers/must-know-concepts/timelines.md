# ⏳ Timelines

```protobuf
message CollectionMetadataTimeline {
  CollectionMetadata collectionMetadata = 1;
  repeated UintRange timelineTimes = 2;
}
```

Certain fields in the BitBadges interface are timeline-based fields. This means that you can set when a field is a certain value based on the current time.

For example, one can set the collection metadata URL to be https://example1.com from January to March and have it automatically switch to https://example2.com for March to December. The switch happens automatically and doesn't need any blockchain transaction to trigger it at that time.

Setting a field to have multiple values at the same time is disallowed (i.e. no duplicate timeline times).

If the current time does not have a corresponding value set, we assume it is empty / null.


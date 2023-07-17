# 🥇 First-Match Only

Throughout the interface, we typically use a first-match only design pattern. Many fields in the interface like permissions, timelines, badge metadata etc are stored in an array, and each element can correspond to different values based on certain criteria.

For example, for badge metadata, we store all metadata for badges in an array. The badge IDs are the criteria that determine what a match is. So when searching for the corresponding URI for a badge ID, we find the element where the badge ID is specified and use the corresponding URI. So for example, badge IDs 1-10 would correspond to uri1 below.

```typescript
[{ uri: uri1, badgeIds: [{ start: 1, end: 10 }], { uri: uri2: { start: 11, end: 100 }] }
```

**First-match only** refers to the resolution process if there are duplicates. In the case there are, we do a linear scan of the array stating with the first element and only take the first time the criteria matches. Subsequent matches are all ignored.

For example, if we have&#x20;

```typescript
[{ uri: uri1, badgeIds: [{ start: 1, end: 100 }], { uri: uri2: { start: 11, end: 100 }] }
```

The first-match for badge IDs 1-100 is uri1, and uri2 would always be ignored.






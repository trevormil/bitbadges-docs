# First Matches

As explained in the For Developers section, certain fields use a first-match system. You can use the following functions to get the first match only for specific fields. Certain functions below also help you handle all possible combinations (i.e. add default, empty value for missing timeline times).

```typescript
export function getFirstMatchForUserIncomingApprovedTransfers(
  approvedTransfers: UserApprovedIncomingTransferWithDetails<bigint>[],
  userAddress: string,
  handleAllPossibleCombinations?: boolean
)
```

```typescript
export function getFirstMatchForUserOutgoingApprovedTransfers(
  approvedTransfers: UserApprovedOutgoingTransferWithDetails<bigint>[],
  userAddress: string,
  handleAllPossibleCombinations?: boolean
)
```

```typescript
function getFirstMatchForCollectionApprovedTransfers(
  collectionApprovedTransfers: CollectionApprovedTransferWithDetails<bigint>[],
  handleAllPossibleCombinations?: boolean
)
```

```typescript
function getFullManagerTimeline(timeline: ManagerTimeline<bigint>[]): ManagerTimeline<bigint>[]
```

```typescript
function getFullCollectionMetadataTimeline(timeline: CollectionMetadataTimeline<bigint>[]): CollectionMetadataTimeline<bigint>[]
```

And so on for all other timelines.

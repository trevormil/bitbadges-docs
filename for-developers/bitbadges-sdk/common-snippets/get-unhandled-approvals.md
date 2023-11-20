# Get Unhandled Approvals

You can use the following functions to get the approval combinations that have unhandled.

```typescript
export function getUnhandledCollectionApprovals(
  collectionApprovals: CollectionApprovalWithDetails<bigint>[],
  doNotMerge?: boolean
)
```

```typescript
export function getUnhandledUserOutgoingApprovals(
  approvals: UserOutgoingApprovalWithDetails<bigint>[],
  userAddress: string,
  doNotMerge?: boolean
)
```

```typescript
export function getUnhandledUserIncomingApprovals(
  approvals: UserIncomingApprovalWithDetails<bigint>[],
  userAddress: string,
  doNotMerge?: boolean
)
```

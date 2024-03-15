# Approvals / Transferability

Documentation Link: [Here](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs) -> Approvals / Transferability

You can use the following functions to get the approval combinations that have unhandled. If unhandled, they are disapproved.

```typescript
export function getUnhandledCollectionApprovals(
  collectionApprovals: CollectionApprovalWithDetails<bigint>[],
  ignoreTrackerIds?: boolean
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

Use the following functions to add the default user approvals to an existing set of approvals. For incoming, this will be: if unhandled, approve only if to == initiatedBy. For outgoing, vice versa.

```typescript
export function appendSelfInitiatedOutgoingApproval(currApprovals: UserOutgoingApprovalWithDetails<bigint>[], userAddress: string): UserOutgoingApprovalWithDetails<bigint>[]
```

```typescript
export function appendSelfInitiatedIncomingApproval(currApprovals: UserIncomingApprovalWithDetails<bigint>[], userAddress: string): UserIncomingApprovalWithDetails<bigint>[] 
```

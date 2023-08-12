# Validate Updates

To validate if an update (old -> new) is valid according to the permissions set, use the following functions.&#x20;

```typescript
export function validateCollectionApprovedTransfersUpdate(
  oldApprovedTransfers: CollectionApprovedTransferTimelineWithDetails<bigint>[],
  newApprovedTransfers: CollectionApprovedTransferTimelineWithDetails<bigint>[],
  canUpdateCollectionApprovedTransfers: CollectionApprovedTransferPermissionWithDetails<bigint>[]
): Error | null
```

And so on for all other field types. They will be named validate + "..." Update.



For permission updates, updating a permission from (old -> new),&#x20;

```typescript
export function validateActionPermissionUpdate(
  oldPermissions: ActionPermission<bigint>[],
  newPermissions: ActionPermission<bigint>[]
): Error | null
```

And so on for all permission types.

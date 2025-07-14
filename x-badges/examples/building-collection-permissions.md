# Building Your Collection Permissions

Collection permissions are executable by the manager. They are used to control who can perform various management actions on your badge collection and when those actions are allowed.

```typescript
const managerTimeline = [
    {
        manager: 'bb1youraddress...', // Your address
        timelineTimes: FullTimeRanges,
    },
];
```

## Setting Your Permissions

You have a few options for setting your permissions.

1. No Manager

If you simply don't want a manager, you can set the manager to an empty string. Then, the permission values never matter.

```typescript
const managerTimeline = [
    {
        manager: '',
        timelineTimes: FullTimeRanges,
    },
];
```

2. Complete Control - Soft Enabled

Each permission is enabled by default, unless you permanently disabled it. Thus, an empty array means that the permission is enabled for all times. However, it is soft enabled, meaning that the manager can disable it at any time. This configuration offers full control with ability to disable in the future.

```typescript
const collectionPermissions = {
    canDeleteCollection: [],
    canArchiveCollection: [],
    canUpdateOffChainBalancesMetadata: [],
    canUpdateStandards: [],
    canUpdateCustomData: [],
    canUpdateManager: [],
    canUpdateCollectionMetadata: [],
    canCreateMoreBadges: [],
    canUpdateBadgeMetadata: [],
    canUpdateCollectionApprovals: [],
    canUpdateValidBadgeIds: [],
};
```

3. Custom Permissions

Oftentimes, you want a little more control over your permissions though.

Each permission follows the same pattern:

1. For the times `permanentlyPermittedTimes`, the permission is always permitted for the given values.
2. For the times `permanentlyForbiddenTimes`, the permission is always forbidden for the given values.
3. If the item is not explicity in either, then the permission is enabled for the given values, but the status can change.

```typescript



```

---

Each permission type follows the same pattern of two categories:

```typescript
// Part 1. Enabled vs Disabled Times For The Execution Of The Permission
const permanentlyPermittedTimes = [];
const permanentlyForbiddenTimes = FullTimeRanges;

// Part 2. For what values (if any) does this apply? This is dependent on the permission type.
const {
    timelineTimes,
    badgeIds,
    fromListId,
    toListId,
    initiatedByListId,
    transferTimes,
    ownershipTimes,
    approvalId,
} = permission;
```

## Common Things To Consider

1.

## Examples

We refer you to the [examples](./permissions/) for more detailed examples.

## Related Concepts

-   [Permission System](../concepts/permissions/permission-system.md)
-   [Manager](../concepts/manager.md)
-   [Timeline System](../concepts/timeline-system.md)
-   [Timed Update Permission](../concepts/permissions/timed-update-permission.md)

```

```

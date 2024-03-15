# Permissions / Updates

Oftentimes, you want to check if a value can be updated, a permission can be executed, etc. There are a couple categories of these types of functions.









Use the following functions to check if an action is executable at the current time. Note this is different from checking if a permission update is correct.

Ex: See if we can create the inputted balances, if the inputted permissions are currently set.

```typescript
export function checkIfBalancesActionPermissionPermits(
  balances: Balance<bigint>[],
  permissions: BalancesActionPermission<bigint>[]
): Error | null
```

Ex: See if we can update the timeline values for these badge IDs, if the inputted permissions are currently set.

```typescript
export function checkIfTimedUpdateWithBadgeIdsPermissionPermits(
  details: {
    timelineTimes: UintRange<bigint>[],
    badgeIds: UintRange<bigint>[],
  }[],
  permissions: TimedUpdateWithBadgeIdsPermission<bigint>[]
): Error | null
```

And so on for all other permission types.

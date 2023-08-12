# Check Permissions

Use the following functions to check if a permission is executable at the current time. Note this is different from updating permissions.

```typescript
export function checkBalancesActionPermission(
  balances: Balance<bigint>[],
  permissions: BalancesActionPermission<bigint>[]
): Error | null
```

```typescript
export function checkTimedUpdateWithBadgeIdsPermission(
  details: {
    timelineTimes: UintRange<bigint>[],
    badgeIds: UintRange<bigint>[],
  }[],
  permissions: TimedUpdateWithBadgeIdsPermission<bigint>[]
): Error | null
```

And so on for all other permission types.

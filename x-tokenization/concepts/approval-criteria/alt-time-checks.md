# Alt Time Checks

Alternative time-based checks for approval denial. If the transfer time falls within any of the specified offline hours or days, the approval is denied.

## Interface

```typescript
interface AltTimeChecks {
    /** Hours (0-23) when transfers should be denied. Uses UTC timezone. */
    offlineHours?: UintRange[];
    /** Days (0-6, where 0=Sunday, 1=Monday, ..., 6=Saturday) when transfers should be denied. Uses UTC timezone. */
    offlineDays?: UintRange[];
}

interface ApprovalCriteria<T extends NumberType> {
    altTimeChecks?: AltTimeChecks;
}
```

## How It Works

Alt time checks provide an additional layer of time-based restrictions beyond the standard `transferTimes` field. While `transferTimes` defines when transfers are **allowed**, `altTimeChecks` defines when transfers are **denied** based on the hour of day or day of week.

### Timezone

All time checks use **UTC timezone** for a neutral, timezone-independent approach. This ensures consistent behavior regardless of where users or validators are located.

### Offline Hours

The `offlineHours` field specifies ranges of hours (0-23) when transfers should be denied:

-   **0** = Midnight (00:00 UTC)
-   **23** = 11 PM (23:00 UTC)
-   Ranges are inclusive (e.g., `{start: 22, end: 2}` covers 22:00, 23:00, 00:00, 01:00, 02:00 UTC)

### Offline Days

The `offlineDays` field specifies ranges of days (0-6) when transfers should be denied:

-   **0** = Sunday
-   **1** = Monday
-   **2** = Tuesday
-   **3** = Wednesday
-   **4** = Thursday
-   **5** = Friday
-   **6** = Saturday

## Evaluation Logic

The approval is **denied** if the transfer time falls within **any** of the specified offline hours or days:

1. Check if the current UTC hour falls within any `offlineHours` range
2. Check if the current UTC day of week falls within any `offlineDays` range
3. If either condition is true, the approval is denied

## Examples

### Block Transfers During Night Hours

Prevent transfers between 10 PM and 6 AM UTC:

```json
{
    "approvalCriteria": {
        "altTimeChecks": {
            "offlineHours": [{ "start": "22", "end": "5" }]
        }
    }
}
```

This blocks transfers from 22:00 UTC (10 PM) through 05:00 UTC (5 AM), covering the night hours.

## Relationship to Transfer Times

Alt time checks work **in addition to** the standard `transferTimes` field:

1. First, the transfer time must fall within the `transferTimes` ranges (if specified)
2. Then, the transfer time must **not** fall within any `offlineHours` or `offlineDays` ranges

If either condition fails, the approval is denied.

## Constraints

-   Hour values must be in the range **0-23** (inclusive)
-   Day values must be in the range **0-6** (inclusive), where 0=Sunday, 6=Saturday
-   All time checks use **UTC timezone**
-   Ranges within the same array should not overlap (though overlapping ranges will still work, they may be redundant)
-   If both `offlineHours` and `offlineDays` are specified, the approval is denied if **either** condition matches

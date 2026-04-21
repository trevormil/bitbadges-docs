# Alt Time Checks

Alternative time-based checks for approval denial. If the transfer time falls within any of the specified offline hours or days, the approval is denied.

## Interface

```typescript
interface AltTimeChecks {
    /** Hours (0-23) when transfers should be denied. */
    offlineHours?: UintRange[];
    /** Days of the week (0-6, where 0=Sunday, 6=Saturday) when transfers should be denied. */
    offlineDays?: UintRange[];
    /** Months (1-12, where 1=January, 12=December) when transfers should be denied. */
    offlineMonths?: UintRange[];
    /** Days of the month (1-31) when transfers should be denied. */
    offlineDaysOfMonth?: UintRange[];
    /** ISO 8601 weeks of the year (1-53) when transfers should be denied. */
    offlineWeeksOfYear?: UintRange[];
    /** Timezone offset magnitude in minutes from UTC, in the range 0-840 (±14 hours). Combined with timezoneOffsetNegative to determine direction. */
    timezoneOffsetMinutes?: Uint;
    /** If true, the timezone offset is west of UTC (negative). If false or unset, east of UTC (positive). */
    timezoneOffsetNegative?: boolean;
}

interface ApprovalCriteria<T extends NumberType> {
    altTimeChecks?: AltTimeChecks;
}
```

## How It Works

Alt time checks provide an additional layer of time-based restrictions beyond the standard `transferTimes` field. While `transferTimes` defines when transfers are **allowed**, `altTimeChecks` defines when transfers are **denied** based on the hour of day, day of week, month, day of month, or week of year.

### Timezone

By default, all time checks use **UTC**. You can optionally configure a timezone offset using `timezoneOffsetMinutes` and `timezoneOffsetNegative`. The chain applies the offset to convert the current UTC time to local time before evaluating all offline checks.

-   `timezoneOffsetMinutes` — the magnitude of the offset in minutes
-   `timezoneOffsetNegative` — set to `true` for timezones west of UTC (negative offsets)

For example, US Eastern Standard Time (EST, UTC-5) is represented as `timezoneOffsetMinutes: 300` and `timezoneOffsetNegative: true`. India Standard Time (IST, UTC+5:30) is `timezoneOffsetMinutes: 330` and `timezoneOffsetNegative: false`.

If no offset is set, UTC is used.

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

### Offline Months

The `offlineMonths` field specifies ranges of months (1-12) when transfers should be denied:

-   **1** = January
-   **2** = February
-   ...
-   **12** = December

### Offline Days of Month

The `offlineDaysOfMonth` field specifies ranges of days within a month (1-31) when transfers should be denied. If a month has fewer days than the specified value (e.g., day 31 in February), those values are simply never matched for that month.

### Offline Weeks of Year

The `offlineWeeksOfYear` field specifies ranges of ISO 8601 weeks (1-53) when transfers should be denied. ISO 8601 defines week 1 as the week containing the first Thursday of the year. Most years have 52 weeks; some have 53 (the next 53-week year is 2026).

## Evaluation Logic

The approval is **denied** if the transfer time falls within **any** of the specified offline ranges. If a timezone offset is configured, the chain first converts the current UTC time to local time by applying the offset.

1. Apply timezone offset (if configured) to get local time
2. Check if the local hour falls within any `offlineHours` range
3. Check if the local day of week falls within any `offlineDays` range
4. Check if the local month falls within any `offlineMonths` range
5. Check if the local day of month falls within any `offlineDaysOfMonth` range
6. Check if the local ISO week falls within any `offlineWeeksOfYear` range
7. If **any** condition is true, the approval is denied

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

### Block Transfers on Weekends for US Eastern Time

Deny weekend transfers using a timezone offset for EST (UTC-5):

```json
{
    "approvalCriteria": {
        "altTimeChecks": {
            "offlineDays": [{ "start": "0", "end": "0" }, { "start": "6", "end": "6" }],
            "timezoneOffsetMinutes": "300",
            "timezoneOffsetNegative": true
        }
    }
}
```

The chain converts the current UTC time to EST before checking the day of week. A transfer at 3 AM UTC on Monday would be evaluated as 10 PM EST on Sunday and therefore denied.

### Block Transfers in December

```json
{
    "approvalCriteria": {
        "altTimeChecks": {
            "offlineMonths": [{ "start": "12", "end": "12" }]
        }
    }
}
```

### Block Transfers on the 1st and 15th of Each Month

```json
{
    "approvalCriteria": {
        "altTimeChecks": {
            "offlineDaysOfMonth": [{ "start": "1", "end": "1" }, { "start": "15", "end": "15" }]
        }
    }
}
```

## Relationship to Transfer Times

Alt time checks work **in addition to** the standard `transferTimes` field:

1. First, the transfer time must fall within the `transferTimes` ranges (if specified)
2. Then, the transfer time must **not** fall within any `offlineHours` or `offlineDays` ranges

If either condition fails, the approval is denied.

## Constraints

-   Hour values must be in the range **0-23** (inclusive)
-   Day-of-week values must be in the range **0-6** (inclusive), where 0=Sunday, 6=Saturday
-   Month values must be in the range **1-12** (inclusive), where 1=January, 12=December
-   Day-of-month values must be in the range **1-31** (inclusive)
-   Week-of-year values must be in the range **1-53** (inclusive), following ISO 8601 (most years end at 52; some end at 53)
-   `timezoneOffsetMinutes` must be a non-negative integer in the range **0-840** (magnitude only; 840 minutes = 14 hours, the widest real-world UTC offset; use `timezoneOffsetNegative` for direction)
-   Ranges within the same array should not overlap (though overlapping ranges will still work, they may be redundant)
-   If multiple offline fields are specified, the approval is denied if **any** condition matches

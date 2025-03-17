# Uint Ranges

<pre class="language-typescript"><code class="lang-typescript"><strong>export interface UintRange&#x3C;T extends NumberType> {
</strong>  start: T;
  end: T;
}
</code></pre>

### Overview

The `UintRange` type is a fundamental component in BitBadges, representing an inclusive range of numbers from a start value to an end value. This type enables powerful range-based operations and is primarily used for badge IDs and time ranges.

### Usage

#### Badge IDs

When transferring badges, specify an array of `UintRange<number>` to indicate which badge IDs to transfer.

Example:

```typescript
const badgeIdsToTransfer: UintRange<number>[] = [
  { start: 1, end: 10 },
  { start: 20, end: 50 }
];
```

#### Time Ranges

`UintRange` is also used for time-based operations, such as `transferTimes`. In this context, the values represent UNIX milliseconds since the epoch.

Example:

```typescript
const transferTimes: UintRange<string>[] = [
  { start: "1630000000000", end: "1640000000000" }
];
// This represents a transfer time range from 2021-08-26 to 2021-12-20
```

### Restrictions

Unless otherwise specified, we only allow numbers in the ranges to be from 1 to Go Max UInt64

* Valid ranges: 1 to 18446744073709551615 (Go's `math.MaxUint64`)
* Zero and values greater than the maximum are not allowed

### Special Cases

#### Full Range

To represent a complete range, use:

```typescript
const fullRange: UintRange<string> = {
  start: "1",
  end: "18446744073709551615"
};
```

#### Range Inversion

Inverting a range results in all values from 1 to 18446744073709551615 that are not in the current range.

# 🔢 Uint Ranges

<pre class="language-typescript"><code class="lang-typescript"><strong>export interface UintRange&#x3C;T extends NumberType> {
</strong>  start: T;
  end: T;
}
</code></pre>

A core type behind the scenes in BitBadges is the [UintRange](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/UintRange.html) type, which simply defines a range of numbers from some start value to some end value, inclusive. This allows us to apply powerful range logic.

These are typically used for representing badge IDs and time ranges. For example, transferring badges will require an UintRange\<number>\[] of badgeIds to be specified. If you say to transfer \[{ start: 1, end: 10}, {start: 20, end: 50}], it will transfer the badge IDs 1-10 and 20-50.

**Restricted Values**

If used for badge IDs or times, we only allow the start and end to be within the range of 1 to Go's math.MaxUint64 or 18446744073709551615 (**so no zero value and no value greater than that**).

To represent a "full" or "complete" range, use \[{ start: 1, end: 18446744073709551615 }]. If we invert a range, we get all the values from 1 to 18446744073709551615 that are not in the current range.

```json
"transferTimes": [
  {
    "start": "1",
    "end": "18446744073709551615"
  }
]
```

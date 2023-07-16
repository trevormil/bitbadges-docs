# 🔢 Uint Ranges

```protobuf
message UintRange {
  string start = 1 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false ];
  string end = 2 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
}
```

A core type behind the scenes in BitBadges is the UintRange type, which simply defines a range of numbers from some start value to some end value, inclusive.&#x20;

**Using UintRanges for Badge IDs and Time Ranges**

These are typically used for representing badge IDs and time ranges. For example, transferring badges will require an UintRange\[] of badgeIds to be specified. If you say to transfer \[{ start: 1, end: 10}, {start: 20, end: 50}], it will transfer the badge IDs 1-10 and 20-50.

If used for badge IDs or times, we only allow the start and end to be within the range of 1 (**so no zero value**) to Go's math.MaxUint64 or 18446744073709551615.&#x20;

To represent a "full" or "complete" range, use \[{ start: 1, end: 18446744073709551615 }].

If we invert a range, we get all the values from 1 to 18446744073709551615  that are not in the current range.

**Handling Duplicate Uints**

If there are overlaps in an array of UintRanges (e.g. \[{ start: 1, end: 10}, {start: 5, end: 50}]), we handle in the following ways:

* If the ranges are part of a [Balance](balances.md) w/ amounts, we add the duplicates twice (see [Duplicate IDs](balances.md)).
* Else, we sort and merge the overlapping ones.

# Uint Ranges

Let's delve into a tutorial that demonstrates how to utilize the provided functions and the `UintRange` interface effectively for manipulating and querying ranges of unsigned integers.

#### Tutorial: Managing and Querying Unsigned Integer Ranges

**1. Introduction to `UintRange`**

The `UintRange` interface captures a range of unsigned integers using a start and end property. This is handy when representing intervals or spans of values.

**2. Sorting and Merging Ranges**

To sort a list of ranges and merge adjacent or overlapping ones:

```typescript
const ranges: UintRange<bigint>[] = [
  { start: 10n, end: 20n },
  { start: 5n, end: 12n },
  { start: 21n, end: 25n }
];

const sortedAndMergedRanges = sortUintRangesAndMergeIfNecessary(ranges);
console.log(sortedAndMergedRanges); // Expected: [{ start: 5n, end: 25n }]
```

**3. Searching Within Ranges**

To search for a specific ID within a list of ranges and return its index and a boolean indicating if it was found:

```typescript
const searchId = 15n;
const [index, isFound] = searchUintRangesForId(searchId, sortedAndMergedRanges);

console.log(`Index: ${index}, Found: ${isFound}`);
```

**4. Inverting Ranges**

To invert a list of ranges between a minimum and maximum ID:

```typescript
const invertedRanges = invertUintRanges(sortedAndMergedRanges, 1n, 30n);
console.log(invertedRanges); // This would show the gaps between the given ranges within the specified bounds.
```

**5. Removing Specific Uints from a Range**

To remove a specific sub-range from a given range:

```typescript
const subRangeToRemove: UintRange<bigint> = { start: 8n, end: 18n };
const originalRange: UintRange<bigint> = { start: 5n, end: 25n };

const resultRange = removeUintsFromUintRange(subRangeToRemove, originalRange);
console.log(resultRange);
```

**6. Removing One Range From Another**

To remove one range from another and also get the removed part:

```typescript
const rangeToRemove = [{ start: 10n, end: 20n }];
const fromRange = { start: 5n, end: 30n };

const [remainingRanges, removedRanges] = removeUintRangeFromUintRange(rangeToRemove, fromRange);
console.log("Remaining:", remainingRanges);
console.log("Removed:", removedRanges);
```

**7. Checking for Overlaps**

To determine if there are overlaps within a list of ranges:

```typescript
const overlappingCheck = checkIfUintRangesOverlap(sortedAndMergedRanges);
console.log(`Ranges Overlap: ${overlappingCheck}`);
```

**Conclusion**

The functions provided offer a comprehensive toolkit for managing and querying unsigned integer ranges. Whether you're checking for overlaps, inverting ranges, or removing specific integers from a range, you now have the tools to do it efficiently and systematically.

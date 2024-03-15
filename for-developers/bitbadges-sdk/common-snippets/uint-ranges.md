# Uint Ranges

Documentation Link: [Here](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs) -> UintRanges



#### Tutorial: Managing and Querying Unsigned Integer Ranges

**1. Introduction to `UintRange`**

The `UintRange` interface captures a range of unsigned integers using a start and end property. This is handy when representing intervals or spans of values.

**2. Sorting and Merging Ranges**

To sort a list of ranges and merge adjacent or overlapping ones:

```typescript
const ranges = UintRangeArray.From([
  { start: 10n, end: 20n },
  { start: 5n, end: 12n },
  { start: 21n, end: 25n }
]);
ranges.sortAndMerge();

console.log(ranges); // Expected: [{ start: 5n, end: 25n }]
```

**3. Searching Within Ranges**

To search for a specific ID within a list of ranges and return its index and a boolean indicating if it was found:

```typescript
const idToSearch = 15n;
const [index, isFound] = ragnes.search(idToSearch)
console.log(`Index: ${index}, Found: ${isFound}`);
```

**4. Inverting Ranges**

To invert a list of ranges between a minimum and maximum ID:

<pre class="language-typescript"><code class="lang-typescript"><strong>ranges.invert(1n, 30n);
</strong>console.log(invertedRanges); // This would show the gaps between the given ranges within the specified bounds.
</code></pre>

**5. Removing One Range From Another**

To remove one range from another and also get the removed part:

```typescript
const rangesToRemove = [{ start: 10n, end: 20n }];

ranges.remove(rangesToRemove);
console.log("Remaining:", ranges);

const [inCurrButNotOther, overlaps, inOtherButNotCurr] = ranges.getOverlapDetails(rangesToRemove)
```

**6. Checking for Overlaps**

To determine if there are overlaps within a list of ranges:

```typescript
const overlaps = ranges.overlaps([{ ...}]);
console.log(`Ranges Overlap: ${overlappingCheck}`);
```

**Conclusion**

The functions provided offer a comprehensive toolkit for managing and querying unsigned integer ranges. Whether you're checking for overlaps, inverting ranges, or removing specific integers from a range, you now have the tools to do it efficiently and systematically.



```typescript
import { GO_MAX_UINT_64, UintRange, UintRangeArray } from 'bitbadgesjs-sdk'

//Singular range functions
const range = new UintRange<bigint>({ start: 1n, end: 10n })
const size = range.size() //10n
const fullRange = UintRange.FullRange() // 1n - GO_MAX_UINT_64
const isFull = fullRange.isFull()
const inverted = range.invert() // 11n - GO_MAX_UINT_64
const overlaps = range.overlaps(inverted) // false
const doesFiveExist = range.search(5n) // true
const overlapDetails = range.getOverlapDetails(fullRange) // [[], [{ start: 1n, end: 10n }], [{ start: 11n, end: GO_MAX_UINT_64 }]]
const overlappingRanges = range.getOverlaps(fullRange) // [{ start: 1n, end: 10n }]

const rangeArr = UintRangeArray.From<bigint>([{ start: 1n, end: 10n }, { start: 11n, end: 20n }])
const rangeArrSize = rangeArr.size() // 20n
const rangeArrFull = UintRangeArray.FullRanges() // 1n - GO_MAX_UINT_64
const rangeArrIsFull = rangeArrFull.isFull()
const rangeArrInverted = rangeArr.toInverted({ start: 1n, end: GO_MAX_UINT_64 }) // 21n - GO_MAX_UINT_64

const unsortedArr = UintRangeArray.From<bigint>([{ start: 11n, end: 20n }, { start: 1n, end: 15n }])
const hasOverlaps = unsortedArr.hasOverlaps() // true
unsortedArr.sortAndMerge() // [{ start: 1n, end: 20n }]

const sortedArr = unsortedArr.clone()
const [inCurrButNotOther, overlaps, inOtherButNotCurr] = sortedArr.getOverlapDetails(unsortedArr) // [[], [{ start: 1n, end: 20n }], []]

sortedArr.remove({ start: 1n, end: 10n }) // [{ start: 11n, end: 20n }]

const [idx, found] = sortedArr.search(11n) // [0n, true]
const exists = sortedArr.searchIfExists(11n) // true
const index = sortedArr.searchIndex(11n) // 0n
```

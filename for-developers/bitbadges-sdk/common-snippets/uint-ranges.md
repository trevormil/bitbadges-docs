# Uint Ranges

Let's delve into a tutorial that demonstrates how to utilize the provided functions and the `UintRange` interface effectively for manipulating and querying ranges of unsigned integers.

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

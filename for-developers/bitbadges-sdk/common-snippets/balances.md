# Balances

### Tutorial: Using the TypeScript SDK for Balance Operations

**1. Define the Balance**

Before utilizing the SDK's functionality, you must define a balance. Here's how you create a balance using the provided `Balance` interface:

<pre class="language-typescript"><code class="lang-typescript"><strong>const userBalance: Balance&#x3C;bigint> = {
</strong>  "amount": 5n, // example badge amount using the BigInt type
  "badgeIds": [{ start: 1n, end: 5n }],
  "ownershipTimes": [{ start: 1628770800000n, end: 1628857200000n }] // example timestamps using BigInt
};
</code></pre>

Note: The `UintRange` type is assumed to be an object with `start` and `end` properties of type `bigint`. Adjust as necessary based on the actual definition.

**2. Add Balance**

To add a balance to an array of existing balances:

```typescript
const balanceToAdd: Balance<bigint> = {
  amount: 3n,
  badgeIds: [{ start: 6n, end: 8n }],
  ownershipTimes: [{ start: 1628860800000n, end: 1628947200000n }]
};

const updatedBalances = addBalance([userBalance], balanceToAdd);
```

This will add `balanceToAdd` to the list of existing balances.

**3. Subtract Balance**

To subtract a balance from an array of existing balances:

```typescript
const balanceToRemove: Balance<bigint> = {
  amount: 2n,
  badgeIds: [{ start: 2n, end: 3n }],
  ownershipTimes: [{ start: 1628784400000n, end: 1628870800000n }]
};

const remainingBalances = subtractBalance(updatedBalances, balanceToRemove);
```

By default, if subtracting the balance causes the amount to go below 0, it will throw an error. However, if you want to allow for negative values (underflow), you can pass `true` as the third argument:

```typescript
const remainingBalancesWithUnderflow = subtractBalance(updatedBalances, balanceToRemove, true);
```

**Conclusion**

This SDK provides a clear and structured way to manage and operate on badge balances. With the `addBalance` and `subtractBalance` functions, you can effortlessly update and maintain badge balances in your application.\
\
Given the new functions you've shared, I'll provide a tutorial snippet for each of them.

### Tutorial: Retrieving Balances Based on Badge ID and Time

**1. Get Balance for a Specific ID and Time**

If you need to retrieve the balance for a specific badge ID and a specific ownership time, you can use the `getBalanceForIdAndTime` function:

```typescript
const badgeIdToLookup = 3n;
const timeToLookup = 1628784400000n;  // example timestamp using BigInt

const specificBalance = getBalanceForIdAndTime(badgeIdToLookup, timeToLookup, balances);

console.log(specificBalance); // This will show the balance for the specified badge ID and time, if found.
```

**2. Get Balances for a Specific Badge ID**

To get all balances associated with a specific badge ID:

```typescript
const badgeIdToLookup = 4n;

const badgeBalances = getBalancesForId(badgeIdToLookup, balances);

console.log(badgeBalances); // This will display all the balances for the given badge ID.
```

**3. Get Balances for a Specific Time**

If you need to retrieve all badge balances for a specific ownership time:

```typescript
const timeToLookup = 1628784400000n;

const timeSpecificBalances = getBalancesForTime(timeToLookup, balances);

console.log(timeSpecificBalances); // This will show all the balances that have the specified ownership time.
```



Alright, given the new function `getBalancesForIds` which retrieves balances for a range of badge IDs and a range of times, let's create a tutorial snippet for it:

4. **Get Balances for Specific Ranges of Badge IDs and Times**

If you need to retrieve balances for a range of badge IDs and a range of ownership times, you can utilize the `getBalancesForIds` function:

```typescript
// Define the range of badge IDs and times you want to look up
const idRangesToLookup: UintRange<bigint>[] = [
    { start: 1n, end: 3n },
    { start: 5n, end: 7n }
];

const timeRangesToLookup: UintRange<bigint>[] = [
    { start: 1628770800000n, end: 1628857200000n },  // example timestamp range using BigInt
    { start: 1628943600000n, end: 1629030000000n }   // another timestamp range
];

// Retrieve the balances
const specificBalances = getBalancesForIds(idRangesToLookup, timeRangesToLookup, balances);

console.log(specificBalances); // This will show the balances that fall within the specified badge ID ranges and time ranges.
```

**Conclusion**

The provided functions in this SDK make it easy to retrieve specific badge balances based on different criteria, such as badge ID and ownership time. Utilize these functions to access and display relevant data as per your application's requirements.

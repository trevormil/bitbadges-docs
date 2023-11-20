# Transfers w/ Increments

#### Tutorial: Handling Batch Transfers with Increments in Badge Balances

**1. Define a Starting Balance**

Let's start by defining a list of initial balances:

```typescript
const startingBalances: Balance<bigint>[] = [{
  amount: 100n,
  badgeIds: [{ start: 1n, end: 100n }],
  ownershipTimes: [{ start: 1628770800000n, end: 1628857200000n }]
}];
```

**2. Define the Batch Transfers with Increments**

For this example, let's assume you have a campaign where you want to distribute badges to a set of addresses in sequential order, and each badge has a unique ID that's incremented by 1:

```typescript
const batchTransfer: TransferWithIncrements<bigint> = {
  fromAddress: "0xYourAddress", // replace with your address
  toAddresses: [], // this will be empty because we're using `toAddressesLength`
  badgeIds: [{ start: 1n, end: 1n }],
  ownershipTimes: [{ start: 1628770800000n, end: 1628857200000n }],
  amount: 1n,
  toAddressesLength: 100n,
  incrementBadgeIdsBy: 1n,
  incrementOwnershipTimesBy: 86400000n // assuming this is 1 day in milliseconds in BigInt form
};
```

**3. Calculate the New Balances After Transfers**

Now, you can use the `getBalancesAfterTransfers` function to calculate the new balances after the batch transfers:

```typescript
const newBalances = getBalancesAfterTransfers(startingBalances, [batchTransfer], true);

console.log(newBalances); // This will show the updated balances after performing the batch transfers.
```

**Conclusion**

The `TransferWithIncrements` type provides a convenient method for handling batch transfers, especially when you need to distribute badges sequentially or when badges have varying ownership times. By combining this with the `getBalancesAfterTransfers` function, you can effortlessly manage and update badge balances in your application.

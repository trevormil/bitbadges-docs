# Transfers

The `TransferWithIncrements` type provides a convenient method for handling batch transfers, especially when you need to distribute badges sequentially or when badges have varying ownership times. By combining this with the `getBalancesAfterTransfers` function, you can effortlessly manage and update badge balances in your application.

```typescript
import { BalanceArray, TransferWithIncrements, getAllBadgeIdsToBeTransferred, getAllBalancesToBeTransferred } from '../packages/bitbadgesjs-sdk'

const startingBalances = BalanceArray.From([
  {
    amount: 100n,
    badgeIds: [{ start: 1n, end: 100n }],
    ownershipTimes: [{ start: 1628770800000n, end: 1628857200000n }],
  },
])

const batchTransfer = new TransferWithIncrements<bigint>({
  from: 'Mint', // replace with your address

  balances: startingBalances,

  toAddresses: [], // this will be empty because we're using `toAddressesLength`
  toAddressesLength: 100n,

  incrementBadgeIdsBy: 1n,
  incrementOwnershipTimesBy: 86400000n, // assuming this is 1 day in milliseconds in BigInt form
})


const allBadgeIds = getAllBadgeIdsToBeTransferred([batchTransfer]) // returns [{ start: 1n, end: 100n }]
const allBalancesToBeTransferred = getAllBalancesToBeTransferred([batchTransfer]) // returns [{ amount: 100n, badgeIds: [{ start: 1n, end: 100n }], ownershipTimes: [{ start: 1628770800000n, end: 1628857200000n }] }
```

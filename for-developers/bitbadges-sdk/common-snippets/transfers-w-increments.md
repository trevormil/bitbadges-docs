# Transfers

The `TransferWithIncrements` type provides a convenient method for handling batch transfers, especially when you need to distribute tokens sequentially or when tokens have varying ownership times. By combining this with the `getBalancesAfterTransfers` function, you can effortlessly manage and update balances in your application.

```typescript
import {
    BalanceArray,
    TransferWithIncrements,
    getAllTokenIdsToBeTransferred,
    getAllBalancesToBeTransferred,
} from '../packages/bitbadgesjs-sdk';

const startingBalances = BalanceArray.From([
    {
        amount: 100n,
        tokenIds: [{ start: 1n, end: 100n }],
        ownershipTimes: [{ start: 1628770800000n, end: 1628857200000n }],
    },
]);

const batchTransfer = new TransferWithIncrements<bigint>({
    from: 'Mint', // replace with your address

    balances: startingBalances,

    toAddresses: [], // this will be empty because we're using `toAddressesLength`
    toAddressesLength: 100n,

    incrementTokenIdsBy: 1n,
    incrementOwnershipTimesBy: 86400000n, // assuming this is 1 day in milliseconds in BigInt form
});

const allTokenIds = getAllTokenIdsToBeTransferred([batchTransfer]); // returns [{ start: 1n, end: 100n }]
const allBalancesToBeTransferred = getAllBalancesToBeTransferred([
    batchTransfer,
]); // returns [{ amount: 100n, tokenIds: [{ start: 1n, end: 100n }], ownershipTimes: [{ start: 1628770800000n, end: 1628857200000n }] }
```

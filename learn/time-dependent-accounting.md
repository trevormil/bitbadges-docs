BitBadges supports time-dependent approvals through the `transferTimes` field, allowing you to gate the flow of tokens based on time. This enables time-dependent functionality at scale, such as subscriptions, unlocks, and vesting.

## Time-Dependent Approvals

The `transferTimes` field in approvals defines when transfers are allowed. This field uses UNIX millisecond timestamps to control transfer windows.

```typescript
const approval = {
    fromListId: 'Mint',
    toListId: 'All',
    transferTimes: [
        {
            start: '1691931600000', // Aug 13, 2023
            end: '1723554000000', // Aug 13, 2024
        },
    ],
    tokenIds: [{ start: '1', end: '100' }],
    approvalId: 'time-gated-mint',
};
```

**Translation:** Transfers are only allowed between Aug 13, 2023 and Aug 13, 2024. Outside this window, transfers are automatically blocked.

## Use Cases

Time-dependent approvals enable powerful use cases:

### Token Unlocks

Control when tokens become transferable after a lock period:

```typescript
const unlockApproval = {
    fromListId: 'Mint',
    toListId: 'investor-address',
    transferTimes: [
        {
            start: '1735689600000', // Unlock date: Jan 1, 2025
            end: '18446744073709551615', // Forever after unlock
        },
    ],
    approvalId: 'vesting-unlock',
};
```

Tokens are locked until the unlock date, then transferable indefinitely.

### Token Vesting

Gradually unlock tokens over time using multiple approval windows:

```typescript
const vestingApprovals = [
    {
        fromListId: 'Mint',
        toListId: 'employee-address',
        transferTimes: [
            {
                start: '1691931600000', // 25% after 1 year
                end: '1723554000000',
            },
        ],
        tokenIds: [{ start: '1', end: '250' }],
        approvalId: 'vesting-year-1',
    },
    {
        fromListId: 'Mint',
        toListId: 'employee-address',
        transferTimes: [
            {
                start: '1723554000000', // 25% after 2 years
                end: '1755090000000',
            },
        ],
        tokenIds: [{ start: '251', end: '500' }],
        approvalId: 'vesting-year-2',
    },
    // ... more vesting periods
];
```

Each approval unlocks a portion of tokens at different times, creating a gradual vesting schedule.

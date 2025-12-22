BitBadges supports time-dependent approvals through the `transferTimes` field, allowing you to gate the flow of tokens based on time. This enables time-dependent functionality at scale, such as subscriptions, unlocks, and vesting.

## Time-Dependent Approvals

The `transferTimes` field in approvals defines when transfers are allowed. This field uses UNIX millisecond timestamps to control transfer windows.

```typescript
const approval: CollectionApproval<bigint> = {
    fromListId: 'Mint',
    toListId: 'All',
    initiatedByListId: 'All',
    transferTimes: [
        {
            start: 1691931600000n, // Aug 13, 2023
            end: 1723554000000n, // Aug 13, 2024
        },
    ],
    tokenIds: [{ start: 1n, end: 100n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'time-gated-mint',
    version: 0n,
    // ... other fields
};
```

**Translation:** Transfers are only allowed between Aug 13, 2023 and Aug 13, 2024. Outside this window, transfers are automatically blocked.

## Use Cases

Time-dependent approvals enable powerful use cases:

### Token Unlocks

Control when tokens become transferable after a lock period:

```typescript
const unlockApproval: CollectionApproval<bigint> = {
    fromListId: 'Mint',
    toListId: 'investor-address',
    initiatedByListId: 'All',
    transferTimes: [
        {
            start: 1735689600000n, // Unlock date: Jan 1, 2025
            end: 18446744073709551615n, // Forever after unlock
        },
    ],
    tokenIds: [{ start: 1n, end: 18446744073709551615n }],
    ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
    approvalId: 'vesting-unlock',
    version: 0n,
    // ... other fields
};
```

Tokens are locked until the unlock date, then transferable indefinitely.

### Token Vesting

Gradually unlock tokens over time using multiple approval windows:

```typescript
const vestingApprovals: CollectionApproval<bigint>[] = [
    {
        fromListId: 'Mint',
        toListId: 'employee-address',
        initiatedByListId: 'All',
        transferTimes: [
            {
                start: 1691931600000n, // 25% after 1 year
                end: 1723554000000n,
            },
        ],
        tokenIds: [{ start: 1n, end: 250n }],
        ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        approvalId: 'vesting-year-1',
        version: 0n,
        // ... other fields
    },
    {
        fromListId: 'Mint',
        toListId: 'employee-address',
        initiatedByListId: 'All',
        transferTimes: [
            {
                start: 1723554000000n, // 25% after 2 years
                end: 1755090000000n,
            },
        ],
        tokenIds: [{ start: 251n, end: 500n }],
        ownershipTimes: [{ start: 1n, end: 18446744073709551615n }],
        approvalId: 'vesting-year-2',
        version: 0n,
        // ... other fields
    },
    // ... more vesting periods
];
```

Each approval unlocks a portion of tokens at different times, creating a gradual vesting schedule.

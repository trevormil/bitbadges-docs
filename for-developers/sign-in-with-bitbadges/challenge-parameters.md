# Ownership Requirements

```typescript
const ownershipRequirements = {
    $and: [
        {
            assets: [
                {
                    chain: 'BitBadges',
                    collectionId: 1n,
                    assetIds: [{ start: 1n, end: 1n }],
                    ownershipTimes: UintRangeArray.FullRanges(),
                    mustOwnAmounts: { start: 0n, end: 0n },
                },
            ],
        },
        {
            assets: [
                {
                    chain: 'BitBadges',
                    collectionId: 2n,
                    assetIds: [{ start: 1n, end: 1n }],
                    ownershipTimes: UintRangeArray.FullRanges(),
                    mustOwnAmounts: { start: 0n, end: 0n },
                },
            ],
        },
    ],
};

const popupParams = {
    ...ownershipRequirements,
};
```

### **Asset Ownership Requirements**

The **assetOwnershipRequirements** uses an $and, $or, and base case schema to allow you to implement custom logical requirements. For $and requirements, all criteria in the array must be satisfied. For $or, one of the criteria in the array needs tobe satisfied. You can implement the "not" case by saying owns x0 of a badge.

```typescript
assetOwnershipRequirements: {
    $or: [
        {
            assets: [
                {
                    chain: 'BitBadges',
                    collectionId: 1n,
                    assetIds: [{ start: 1n, end: 1n }],
                    ownershipTimes: UintRangeArray.FullRanges(),
                    mustOwnAmounts: { start: 0n, end: 0n },
                },
            ],
        },
        {
            assets: [
                {
                    chain: 'BitBadges',
                    collectionId: 2n,
                    assetIds: [{ start: 1n, end: 1n }],
                    ownershipTimes: UintRangeArray.FullRanges(),
                    mustOwnAmounts: { start: 1n, end: 1n },
                },
            ],
        },
    ];
}
```

**Options**

As an alternative to $or, we also support specifying **options.numMatchesForVerification** which sets a threshold for how many assets in the current group the criteria needs to pass for. For example, below requires 1 / 1000 badges to be owned out of the IDs 1-1000.

```typescript
assetOwnershipRequirements: {
  assets: [
    {
      chain: 'BitBadges',
      collectionId: 1n,
      assetIds: [{ start: 1n, end: 1000n }],
      ownershipTimes: UintRangeArray.FullRanges(),
      mustOwnAmounts: { start: 1n, end: 1n }
    }
  ],
  options: { numMatchesForVerification: 1n }
}
```

**BitBadges Badge Collections**

For BitBadges assets, we expect the chain = ' BitBadges', all collection IDs to be numeric, and all assetIds to be UintRanges. Querying a user owns a badge at a specific time is also supported via ownership times.

```typescript
{
  chain: 'BitBadges',
  collectionId: 1n,
  assetIds: [{ start: 1n, end: 1000n }],
  ownershipTimes: UintRangeArray.FullRanges(),
  mustOwnAmounts: { start: 0n, end: 0n }
}
```

**BitBadges Address Lists**

For BitBadges address lists, they are supported with the collection ID = 'BitBadges Lists'. The assetIds will be the string list ID. A user will be considered to own x1 if they are on the list and x0 if they are not on the list. Note that with blacklists, they are flipped. Not being on a blacklist equals x1 owned.

```typescript
{
    chain: 'BitBadges',
    collectionId: 'BitBadges Lists',
    assetIds: ["listId"],
    ownershipTimes: UintRangeArray.FullRanges(),
    mustOwnAmounts: { start: 1n, end: 1n }
}
```

**Ethereum / Polygon / Solana NFTs (Beta)**

We also support verifying Ethereum Polygon NFTs through this interface. However, note that we use external APIs to check this, so is not reliant on our infrastructure. Use at your own risk.

```typescript
{
    $and: [
        {
            assets: [
                {
                    chain: 'Polygon', //Or 'Ethereum'
                    collectionId: '0x9a7f0b7d4b6c1c3f3b6d4e6d5b6e6d5b6e6d5b6e',
                    assetIds: ['1'],
                    ownershipTimes: [],
                    mustOwnAmounts: { start: 0n, end: 0n },
                },
            ],
        },
    ];
}
```

### **Ownership Times** <a href="#ownership-times" id="ownership-times"></a>

```
ownershipTimes: []
```

The default when ownership times is empty or missing is to verify at the current time. If this is the case, we dynamically add the current time as \[{ start: currTime, end: currTime }].&#x20;

```typescript
ownershipTimes: UintRangeArray.FullRanges();
```

For assets that support ownership times like BitBadges badges, you can specify custom times to check.

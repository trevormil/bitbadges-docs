# Fetching Balances

There are a couple ways to fetch badge balances, depending on the [balance type](../../core-concepts/balances-transfers/balance-types.md) of the collections you are trying to fetch. Note this is different from $BADGE (see [here](fetching-balances.md) for fetching $BADGE balance).

### Fetching by User or by Collection

If you want to fetch badges owned by a specific user or specific owners for a collection, we refer you to the prior tutorials. Notably, you can fetch a paginated view using the **badgesCollected** view key for accounts or the **owners** view key for a collection. These are paginated, so you will need to pass in the prior bookmark received to the next request to fetch the next page (if it has more).

However, note that these only apply to standard on-chain or off-chain indexed balanced. Off-chain non-indexed balances must be fetched directly on demand (see below). You can also fetch off-chain indexed balances directly from source.

{% content-ref url="fetching-collections.md" %}
[fetching-collections.md](fetching-collections.md)
{% endcontent-ref %}

{% content-ref url="fetching-accounts.md" %}
[fetching-accounts.md](fetching-accounts.md)
{% endcontent-ref %}

### Fetching Directly

You can also fetch balances directly using the getBadgeBalanceByAddress API route. This will work for all balance types (standard, off-chain indexed, and off-chain non-indexed).

```typescript
const res = await BitBadgesApi.getBadgeBalanceByAddress(collectionId, address);
console.log(res);
```

### Verifying Requirements

If you do not need the balances themselves and you just want to verify if a user meets certain ownership requirements, consider using the following route which supports custom AND / OR verification checkers.

```typescript
const res = await BitBadgesApi.verifyOwnershipRequirements({
    bitbadgesAddress,
    assetOwnershipRequirements: {
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
    },
});
console.log(res);
```

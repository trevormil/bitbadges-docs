# Fetching Balances

There are a couple ways to fetch badge balances, depending on the [balance type](../../core-concepts/balance-types.md) of the collections you are trying to fetch. Note this is different from $BADGE (see [here](fetching-balances.md) for fetching $BADGE balance.

### Fetching by User or by Collection

If you want to fetch badges owned by a specific user or specific owners for a collection, we refer you to the prior tutorials. Notably, you can fetch a paginated view using the **badgesCollected** view key for accounts or the **owners** view key for a collection. These are paginated, so you will need to pass in the prior bookmark received to the next request to fecth the next page (if it has more).

However, note that these only apply to standard on-chain or off-chain indexed balanced. Off-chain non-indexed balances must be fetched directly (see below)

{% content-ref url="fetching-collections.md" %}
[fetching-collections.md](fetching-collections.md)
{% endcontent-ref %}

{% content-ref url="fetching-accounts.md" %}
[fetching-accounts.md](fetching-accounts.md)
{% endcontent-ref %}

```typescript
const mintBalances: Balance<bigint>[] = collection.owners.find(x => x.cosmosAddress == 'Mint');
```

### Fetching Directly

You can also fetch balances directly using the getBadgeBalanceByAddress API route. This will work for all balance types (standard, off-chain indexed, and off-chain non-indexed).

```typescript
const res = await BitBadgesApi.getBadgeBalanceByAddress(collectionId, cosmosAddress);
console.log(res);
```

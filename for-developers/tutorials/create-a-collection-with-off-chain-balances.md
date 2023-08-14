# Create a Collection with Off-Chain Balances

**Step 1: Create Your Map**

The map is simply a cosmosAddress -> Balance\<NumberType>\[] map. You can create this yourself by using the **OffChainBalancesMap\<NumberType>** type.



You may also find the [**createBalanceMapForOffChainBalances**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/functions/createBalanceMapForOffChainBalances.html) function helpful.

```typescript
const transfers: TransferWithIncrements<bigint>[] = [...];

const balanceMap = await createBalanceMapForOffChainBalances(transfers);
```



Note that you should not allocate more badges in this map than what was created on-chain.

**Step 2: Host your map as a JSON file via some URL**

This can be via IPFS or any method of your choice.&#x20;



Note permanent storage (i.e. IPFS) coupled with not being able to update the URL will make your balances permanent. This can be a good option if you want your collection to enforcably be frozen.

**Step 3: Create Your Collection**

Create your collection and specify your URL via the **offChainBalancesMetadata** timeline. Decide whether you want to be able to update the URL in the future or not, and also set the **canUpdateOffChainBalancesMetadata** permission accordingly.&#x20;



Note you can specify your URL via the form when creating / updating collections on the BitBadges website. Or, you can custom create your collection.



**Step 4: Refreshes / Updates**

If you are allowed to update the URL according to the permissions set, you can do so via another MsgUpdateCollection transaction. This will auto-trigger a refresh on the BitBadges API / indexer as well.



You can also just update the balances returned by the URL as well. Note that the BitBadges indexer / website caches balances. To trigger a refetch, you can use the **POST /api/v0/refresh** enpoint.

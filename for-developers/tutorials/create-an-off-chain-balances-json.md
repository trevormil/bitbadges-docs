# Create an Off-Chain Balances JSON

**Step 1: Create Your Map**

The map is simply a cosmosAddress/mappingId -> Balance\<NumberType>\[] map. You can create this yourself by using the **OffChainBalancesMap\<NumberType>** type.

Note that if you use address mapping IDs for the keys ([see here to learn more](../concepts/address-mappings-lists.md)), the corresponding address mapping must be a whitelist (includeAddresses = false) and should be stored on-chain for reproducability (not off-chain via the BitBadges servers or somewhere else). The BitBadges indexer / API will throw an error in the above cases.

You may also find the [**createBalanceMapForOffChainBalances**](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/functions/createBalanceMapForOffChainBalances.html) function helpful.

```typescript
const transfers: TransferWithIncrements<bigint>[] = [...];

const balanceMap = await createBalanceMapForOffChainBalances(transfers);
```

Note that you should not allocate more badges in this map than what was created on-chain (via the "Mint" address).&#x20;

See [https://bafybeiav4hvef6co2sfpmldqn24tmjzrzfbi6d74q5n2sbks4rhnativbq.ipfs.dweb.link/](https://bafybeiav4hvef6co2sfpmldqn24tmjzrzfbi6d74q5n2sbks4rhnativbq.ipfs.dweb.link/) for an example.

**Step 2: Host your map as a JSON file via some URL**

This can be via IPFS or any method of your choice.&#x20;

Note permanent storage (i.e. IPFS) coupled with not being able to update the URL will make your balances permanent and immutable. This can be a good option if you want your collection to enforcably be frozen and immutable.

**Step 3: Create Your Collection**

Create your collection and specify your URL via the **offChainBalancesMetadata** timeline. Decide whether you want to be able to update the URL in the future or not, and also set the **canUpdateOffChainBalancesMetadata** permission accordingly. Do not overallocate via **badgesToCreate**.



Note you can specify your URL manually via the form when creating / updating collections on the BitBadges website. Or, you can custom create your collection completely on your own.

**Step 4: Refreshes / Updates**

If you are allowed to update the URL according to the permissions set, you can do so via another MsgUpdateCollection transaction. This will auto-trigger a refresh on the BitBadges API / indexer as well.



You can also just update the balances JSON returned by the URL as well without interacting with the blockchain. Note that the BitBadges indexer / website caches balances. To trigger a refetch, you can use the **POST /api/v0/refresh** enpoint or do it directly on the website.




# Overview

For off-chain balances, the badge collection will be stored on the blockchain, but all balances will be assigned off-chain. This allows you to have complete control over assignment of the balances at no cost and no transactions required. You can integrate with any application (even non-crypto ones).

Examples: &#x20;

* Spreadsheets - Assign badges based on addresses in a spreadsheet.
* Subscriptions - Set up your own subscription service and send badges to subscribers!
* Custom Integrations - Integrate with any app you want!

There are two types of off-chain balances: indexed and non-indexed. See the [balances type documentation](../core-concepts/balances-transfers/balance-types.md) for more information. For both options, you must create or have a collection with the desired balances type. The recommended way to create a collection is via the Create form on the BitBadges app. You will be able to enter all self-hosted details (your URL) directly into the form.

**Refresh Queue**

Note that while balance updates on your site will be instant. BitBadges uses a queue-like system. Upon a refresh or changes detected, the balance update will get added to the queue. This may take some time to fully populate on the site.

**Claim / Assignment System**

Self-hosted balances can be customized to be assigned for anything. You can setup your own assignment process or connect it to a claiming tool (e.g. when a user does something, update their balances). This is all left up to you.

Note that self-hosted balances are not compatible with BitBadges claims. For BItBadges claims, the balances must be stored and maintained by BitBadges. One workaround is to use BitBadges claims for the claiming process and then migrate to a self-hosted option.

**URL Requirements**

The URLs should be a GET endpoints accessible to whoever needs it (e.g. BitBadges indexer). It is your responsibility to handle CORS errors and such yourself.

### Non-Indexed Balances (On-Demand)

For non-indexed balances, you simply need to set up a server which can return the current balances for a specified Cosmos address.

Couple notes:

* The URL stored on-chain must have {address} as a placeholder for the address to query.
* The URL param is expected to support converted Cosmos addresses. It is up to you whether you want to support native addresses as well, but converted Cosmos address support is mandatory. See [here for more information](../accounts.md).

Example:

On-Chain URL: "http://localhost:3000/nonIndexed/{address}"

```typescript
app.get('/nonIndexed/:address', async (req, res) => {
  const address = req.params.address; 
  const cosmosAddress = convertToCosmosAddress();
  
  //custom logic - (e.g. check subscription status or check some local DB value)

  const balances: Balance<bigint>[] = [...];
  return res.status(200).send({ balances });
});
```

### Indexed Balances

With indexed balances, you store and host the entire balance map all at one endpoint.

**Step 1: Create Your Map**

The map is simply a cosmosAddress/listId -> Balance\<NumberType>\[] map. You can create this yourself by using the **OffChainBalancesMap\<NumberType>** type.

Note that if you use address list IDs for the keys ([see here to learn more](../core-concepts/address-lists-lists.md)), the corresponding address list must be a whitelist (whitelist = false) and should be stored on-chain for reproducability (not off-chain via the BitBadges servers or somewhere else). You should also not allocate more badge IDs in this map than what was created on-chain (via the "Mint" address).

You may also find the [**createBalanceMapForOffChainBalances**](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs/functions/createBalanceMapForOffChainBalances.html) function helpful.

```typescript
const transfers: TransferWithIncrements<bigint>[] = [...];

const balanceMap = await createBalanceMapForOffChainBalances(transfers);
```

For example,

```json
{
  "cosmos1qjgpfmk93lqdak3ea7xqp5ec6v8nd79krktww4": [
    {
      "amount": "1",
      "badgeIds": [
        {
          "start": "1",
          "end": "1"
        }
      ],
      "ownershipTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ]
    }
  ],
  "cosmos1zd5dsage58jfrgmsu377pk6w0q5zhc67fn4gsl": [
    {
      "amount": "1",
      "badgeIds": [
        {
          "start": "1",
          "end": "1"
        }
      ],
      "ownershipTimes": [
        {
          "start": "1",
          "end": "18446744073709551615"
        }
      ]
    }
  ]
}
```

**Step 2: Host your map as a JSON file via some URL**

This can be via IPFS or any method of your choice.

Note permanent storage (i.e. IPFS) coupled with not being able to update the URL on-chain will make your balances permanent and immutable. This can be a good option if you want your collection to be frozen, non-transferable, and immutable.

**Step 3: Create Your Collection**

Create your collection and specify your URL via the **offChainBalancesMetadata** timeline. Decide whether you want to be able to update the URL in the future or not. Set the **managerTimeline** and **canUpdateOffChainBalancesMetadata** permission accordingly.

**Future Refreshes / Updates**

In the future, if you are allowed to update the URL on-chain you can do so via another MsgUpdateCollection transaction. This will auto-trigger a refresh on the BitBadges API / indexer as well.

You can also just update the balances JSON returned by the URL as well without interacting with the blockchain (i.e. URL stays the same). Note that the BitBadges indexer / website caches balances. To trigger a refetch manually, you can use the **POST /api/v0/refresh** endpoint or do it directly on the website.

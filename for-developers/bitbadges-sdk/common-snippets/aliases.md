# Aliases

Aliases are random unusable addresses from some seed randomness. They are slightly longer than traditional Cosmos addresses and have no corresponding private key. These are the same as the **aliasAddress** field returned from the API collections and accounts.

See below for how we generate badge, collection, and list aliases.

```typescript
const badgeAlias = generateAlias('badges', getAliasDerivationKeysForBadge(1n, 10000n))
const collectionAlias = generateAlias('collections', getAliasDerivationKeysForCollection(1n))
const listAlias = generateAlias('lists', getAliasDerivationKeysForCollection(1n))
// bb1u4xn6kst47y2hgl3532emz5dlg93te3dhdqsgwucpxu560u8zflqk7f6qm
```


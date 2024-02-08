# MsgDeleteCollection

Deleting a collection is self-explanatory. The manager can delete the collection with [MsgDeleteCollection](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs/interfaces/MsgDeleteCollection.html) as long as they have the permission to do so (the **canDeleteCollection** permission).&#x20;

Deleting a collection will wipe it from the blockchain entirely.

```typescript
export interface MsgDeleteCollection<T extends NumberType> {
  creator: string
  collectionId: T
}
```

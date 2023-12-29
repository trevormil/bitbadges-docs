# MsgSetCollectionForProtocol

MsgSetCollectionForProtocol sets the specific collection ID for the creator. For example, set my BitBadges Follow Protocol collection to collection ID 12.

If collectionId === 0, we use the last created collection. This can be leveraged in multi-msg transactions where you want to create a collection and set the ID to the just created collection. Typically, you do not know the collection ID until after the collection is created which is why this feature is useful.

```typescript
export interface MsgSetCollectionForProtocol<T extends NumberType> {
  creator: string,
  name: string,
  collectionId: T
}
```

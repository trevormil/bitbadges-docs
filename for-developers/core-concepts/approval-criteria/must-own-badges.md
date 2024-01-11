# Must Own Badges

Must own badges are another unique feature that is very powerful. This allows you to specify certain badges and amounts of badges of a collection (typically a different collection) that must be owned at custom times in order to be approved.

For example, you may implement a badge collection where only holders of a verified badge are approved to send and receive badges. Or, you may implement what you must NOT own (own x0) a scammer badge in order to interact.

Note that collections with "Off-Chain" balances are not supported for this feature because the blockchain does not have access to off-chain balances.

```typescript
export interface MustOwnBadges<T extends NumberType> {
  collectionId: T;

  amountRange: UintRange<T>; //min/max amount expected to be owned
  ownershipTimes: UintRange<T>[];
  badgeIds: UintRange<T>[];
  
  //override ownershipTimes with the exact block millisecond at execution
  //Ex: [{start: 12345, end: 12345}]
  overrideWithCurrentTime: boolean;
  
  //if true, must meet ownership requirements for ALL badges
  //if false, must meet ownership requirements for ONE badge
  mustSatisfyForAllAssets: boolean; 
}
```

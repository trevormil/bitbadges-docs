# MsgUpdateCollection

For updating a collection's details on-chain, you can use [MsgUpdateCollection](https://bitbadges.github.io/bitbadgesjs/classes/MsgUpdateCollection.html). This is only executable by the manager, and all updates must obey the previously set permissions.

Note that if you only want to update off-chain balances stored at a URI and nothing else (URI stays the same), nothing on the blockchain will change, and this can all be facilitated off-chain (doesn't require this Msg).

```typescript
export interface MsgUpdateCollection<T extends NumberType> {
    creator: string;
    collectionId: T; //0 for new collections (will be assigned)

    //Note no defaults or balances types here because that is only for new collections

    badgeIdsToAdd?: UintRange<T>[];

    updateCollectionPermissions?: boolean;
    collectionPermissions?: CollectionPermissions<T>;
    updateManagerTimeline?: boolean;
    managerTimeline?: ManagerTimeline<T>[];
    updateCollectionMetadataTimeline?: boolean;
    collectionMetadataTimeline?: CollectionMetadataTimeline<T>[];
    updateBadgeMetadataTimeline?: boolean;
    badgeMetadataTimeline?: BadgeMetadataTimeline<T>[];
    updateOffChainBalancesMetadataTimeline?: boolean;
    offChainBalancesMetadataTimeline?: OffChainBalancesMetadataTimeline<T>[];
    updateCustomDataTimeline?: boolean;
    customDataTimeline?: CustomDataTimeline<T>[];
    updateCollectionApprovals?: boolean;
    collectionApprovals?: CollectionApproval<T>[];
    updateStandardsTimeline?: boolean;
    standardsTimeline?: StandardsTimeline<T>[];
    updateIsArchivedTimeline?: boolean;
    isArchivedTimeline?: IsArchivedTimeline<T>[];
}
```

**Update Flags**

We use an update flag + new value format. If the update flag is true, we will update it to the new value. If it is false, we do not update and ignore the value in the Msg and keep the currently set value.

**Permissions**

**All updates must obey the previously set permissions.** By previously set, we mean the ones before the Msg was started. If you update the permissions in the current Msg, those are applied last and will not be applicable until the following transaction.

**Rest of Fields**

For the rest of the fields, we refer you to the following section.

# MsgUniversalUpdateCollection

MsgUniversalUpdateCollection is a universal message that supports both creating and updateing collections. If collection ID == 0. This is mainly used for legacy purposes. We recommend using MsgCreateCollection and MsgUpdateCollection instead, respectively. We refer you to their documentation for further details.

```typescript
interface MsgUniversalUpdateCollection {
    badgeMetadataTimeline?: BadgeMetadataTimeline<T>[];
    badgesToCreate?: Balance<T>[];
    balancesType?: string;
    collectionApprovals?: CollectionApproval<T>[];
    collectionId: T;
    collectionMetadataTimeline?: CollectionMetadataTimeline<T>[];
    collectionPermissions?: CollectionPermissions<T>;
    creator: string;
    customDataTimeline?: CustomDataTimeline<T>[];
    defaultAutoApproveSelfInitiatedIncomingTransfers?: boolean;
    defaultAutoApproveSelfInitiatedOutgoingTransfers?: boolean;
    defaultIncomingApprovals?: UserIncomingApproval<T>[];
    defaultOutgoingApprovals?: UserOutgoingApproval<T>[];
    defaultUserPermissions?: UserPermissions<T>;
    isArchivedTimeline?: IsArchivedTimeline<T>[];
    managerTimeline?: ManagerTimeline<T>[];
    offChainBalancesMetadataTimeline?: OffChainBalancesMetadataTimeline<T>[];
    standardsTimeline?: StandardsTimeline<T>[];
    updateBadgeMetadataTimeline?: boolean;
    updateCollectionApprovals?: boolean;
    updateCollectionMetadataTimeline?: boolean;
    updateCollectionPermissions?: boolean;
    updateCustomDataTimeline?: boolean;
    updateIsArchivedTimeline?: boolean;
    updateManagerTimeline?: boolean;
    updateOffChainBalancesMetadataTimeline?: boolean;
    updateStandardsTimeline?: boolean;
}
```
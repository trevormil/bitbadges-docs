# MsgCreateCollection

Collections are initially created via [MsgCreateCollection](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/MsgCreateCollection.html) . The interface looks as follows.&#x20;

```typescript
export interface MsgCreateCollection<T extends NumberType> {
  creator: string
  
  //collectionId
  
  balancesType?: string //"Standard" | "Off-Chain"
  
  defaultOutgoingApprovals?: UserOutgoingApproval<T>[]
  defaultIncomingApprovals?: UserIncomingApproval<T>[]
  defaultUserPermissions?: UserPermissions<T>
  defaultAutoApproveSelfInitiatedOutgoingTransfers?: boolean
  defaultAutoApproveSelfInitiatedIncominggTransfers?: boolean
  
  badgesToCreate?: Balance<T>[]
  
  collectionPermissions?: CollectionPermissions<T>
  managerTimeline?: ManagerTimeline<T>[]
  collectionMetadataTimeline?: CollectionMetadataTimeline<T>[]
  badgeMetadataTimeline?: BadgeMetadataTimeline<T>[]
  offChainBalancesMetadataTimeline?: OffChainBalancesMetadataTimeline<T>[]
  customDataTimeline?: CustomDataTimeline<T>[]
  collectionApprovals?: CollectionApproval<T>[]
  standardsTimeline?: StandardsTimeline<T>[]
  isArchivedTimeline?: IsArchivedTimeline<T>[]
}
```

The collectionId will be assigned at execution time and is obtainable in the transaction response. Subsequent updates to the collection will be through MsgUpdateCollection.

**Creation Only Properties**

The creation or genesis transaction for a collection is unique in a couple ways.

1. There are no permissions previously set, so there are no restrictions for what can be set vs not. Subsequent updates to the collection must follow any previously set permissions.
2. This is the only time that you can specify **balancesType** and the default user approval information (**defaultOutgoingApprovals, defaultIncomingApprovals, defaultAutoApproveSelfInitiatedOutgoingTransfers**, **defaultAutoApproveSelfInitiatedIncomingTransfers**, and **defaultUserPermissions**)**.**&#x20;

**Rest of Fields**

For the rest of the fields, we refer you to the following section.

{% content-ref url="../collection-interface/" %}
[collection-interface](../collection-interface/)
{% endcontent-ref %}

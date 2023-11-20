# Creating a Collection

Collections are initially created via MsgCreateCollection. The interface looks as follows.&#x20;

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

  //note there are no update flags like in update msgs
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

**Creation Only Options**

The creation or genesis transaction for a collection is unique in a couple ways.

1. There are no permissions previously set, so there are no restrictions for what can be set vs not. Subsequent updates to the collection must follow any previously set permissions.
2. This is the only time that you can specify **balancesType** and the default user approval information (**defaultOutgoingApprovals, defaultIncomingApprovals, defaultAutoApproveSelfInitiatedOutgoingTransfers**, **defaultAutoApproveSelfInitiatedIncomingTransfers**, and **defaultUserPermissions**)**.**&#x20;

**Creating Badges**

{% content-ref url="../concepts/creating-badges.md" %}
[creating-badges.md](../concepts/creating-badges.md)
{% endcontent-ref %}

**Rest of Fields**

For the rest of the fields, we refer you to the Concepts section.

{% content-ref url="../concepts/" %}
[concepts](../concepts/)
{% endcontent-ref %}

**Example**


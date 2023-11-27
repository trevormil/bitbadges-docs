# 📄 Collection Interface

Collections are the core type in BitBadges. They are identified by a collectionId assigned by the blockchain and define everything about the badges in it, including permissions, metadata, and more!

```typescript

/**
 * CollectionInfoBase is the type of document stored in the collections database (see documentation for more info)
 *
 * @category API / Indexer
 * @typedef {Object} CollectionInfoBase
 * @property {NumberType} collectionId - The collection ID
 * @property {CollectionMetadataTimeline[]} collectionMetadataTimeline - The collection metadata timeline
 * @property {BadgeMetadataTimeline[]} badgeMetadataTimeline - The badge metadata timeline
 * @property {string} balancesType - The type of balances (i.e. "Standard", "Off-Chain", "Inherited")
 * @property {OffChainBalancesMetadataTimeline[]} offChainBalancesMetadataTimeline - The off-chain balances metadata timeline
 * @property {CustomDataTimeline[]} customDataTimeline - The custom data timeline
 * @property {ManagerTimeline[]} managerTimeline - The manager timeline
 * @property {CollectionPermissions} collectionPermissions - The collection permissions
 * @property {CollectionApproval[]} collectionApprovals - The collection approved transfers timeline
 * @property {StandardsTimeline[]} standardsTimeline - The standards timeline
 * @property {IsArchivedTimeline[]} isArchivedTimeline - The is archived timeline
 * @property {UserOutgoingApproval[]} defaultUserOutgoingApprovals - The default user approved outgoing transfers
 * @property {UserIncomingApproval[]} defaultUserIncomingApprovals - The default user approved incoming transfers timeline
 * @property {UserPermissions} defaultUserPermissions - The default user permissions
 * @property {string} createdBy - The cosmos address of the user who created this collection
 * @property {NumberType} createdBlock - The block number when this collection was created
 */
export interface CollectionInfoBase<T extends NumberType> {
  collectionId: T;
  collectionMetadataTimeline: CollectionMetadataTimeline<T>[];
  badgeMetadataTimeline: BadgeMetadataTimeline<T>[];
  balancesType: "Standard" | "Off-Chain" | "Inherited";
  offChainBalancesMetadataTimeline: OffChainBalancesMetadataTimeline<T>[];
  customDataTimeline: CustomDataTimeline<T>[];
  managerTimeline: ManagerTimeline<T>[];
  collectionPermissions: CollectionPermissions<T>;
  collectionApprovals: CollectionApproval<T>[];
  standardsTimeline: StandardsTimeline<T>[];
  isArchivedTimeline: IsArchivedTimeline<T>[];
  defaultUserOutgoingApprovals: UserOutgoingApproval<T>[];
  defaultUserIncomingApprovals: UserIncomingApproval<T>[];
  defaultAutoApproveSelfInitiatedOutgoingTransfers: boolean;
  defaultAutoApproveSelfInitiatedIncomingTransfers: boolean;
  defaultUserPermissions: UserPermissions<T>;
  
  //Added by the API / SDK (not on blockchain)
  createdBy: string;
  createdBlock: T;
  createdTimestamp: T;
  updateHistory: {
    txHash: string;
    block: T;
    blockTimestamp: T;
  }[];
}
```

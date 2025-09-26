# MsgCreateMap

MsgCreateMap creates a map on-chain. We refer you to here for more information on each individual field.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

```typescript
export interface iMsgCreateMap<T extends NumberType> {
  creator: string;
  mapId: string;

  inheritManagerTimelineFrom: T;
  managerTimeline: iManagerTimeline<T>[];

  updateCriteria: iMapUpdateCriteria<T>;
  valueOptions: iValueOptions;
  defaultValue: string;

  metadataTimeline: iMapMetadataTimeline<T>[];

  permissions: iMapPermissions<T>;
}
```

```typescript
export interface iValueOptions {
  noDuplicates: boolean;
  permanentOnceSet: boolean;
  expectUint: boolean;
  expectBoolean: boolean;
  expectAddress: boolean;
  expectUri: boolean;
}
```

```typescript
export interface iMapPermissions<T extends NumberType> {
  canUpdateMetadata: iTimedUpdatePermission<T>[];
  canUpdateManager: iTimedUpdatePermission<T>[];
  canDeleteMap: iActionPermission<T>[];
}
```

```typescript
export interface iMapMetadataTimeline<T extends NumberType> {
  timelineTimes: iUintRange<T>[];
  metadata: iCollectionMetadata;
}
```

```typescript
export interface iMapUpdateCriteria<T extends NumberType> {
  managerOnly: boolean;
  collectionId: T;
  creatorOnly: boolean;
  firstComeFirstServe: boolean;
}
```

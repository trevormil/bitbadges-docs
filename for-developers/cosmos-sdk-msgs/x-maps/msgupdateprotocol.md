# MsgUpdateMap

MsgUpdateMap updates the map with the matching ID. Similar to the x/badges Msgs, we use an update flag + value interface. If update flag = true, we update with the corresponding value. Else, we ignore it.

```typescript
export interface iMsgUpdateMap<T extends NumberType> {
  creator: string;
  mapId: string;

  updateManagerTimeline: boolean;
  managerTimeline: iManagerTimeline<T>[];

  updateMetadataTimeline: boolean;
  metadataTimeline: iMapMetadataTimeline<T>[];

  updatePermissions: boolean;
  permissions: iMapPermissions<T>;
}
```

# Maps / Protocols

Maps are similar to anchors, but they allow you to store data on-chain in a structured way. They are simply key-value maps, and the configuration can be set with customization options like "no duplicates", "expect integere values"", and so on. With maps, you can create universal, reusable, flexible protocols for your users.

```json
{
    "bb...1234": "English",
    "bb...5678": "Spanish"
}
```

```json
{
    "BitBadges Follow Protocol": 12, //collection ID 12 is used for my follows
    "Experiences Protocol": 13
}
```

This is all facilitated through the x/maps module and its correspondingMsgs. See the Msgs for more information.

```typescript
export interface iMap<T extends NumberType> {
    creator: string;
    mapId: string;
    inheritManagerTimelineFrom: T;
    managerTimeline: iManagerTimeline<T>[];
    updateCriteria: iMapUpdateCriteria<T>;
    valueOptions: iValueOptions;
    defaultValue: string;
    permissions: iMapPermissions<T>;
    metadataTimeline: iMapMetadataTimeline<T>[];
}
```

**Reserved Maps**

All maps are identified by a **mapId.** The following **mapId** values are reserved:

-   Any valid Bech32 BitBadges address - These are reserved for maps that can only be created by that specific address. This can be a place to store important values custom to you (that address).
-   Any numeric ID is reserved for the corresponding collection with a matching ID. This can only be created by the collection manager. This can be used to store core details that belong on-chain for the collection not handled by the core collection interface.

**Use Cases**

-   Alternative permissions - Store other permissions related to a collection in these maps on-chain
-   Router for important information - For example, lets say thte collection has credentials attached to it. This can point to where the credentials can be found or important info needed to verify them.
-   Protocols - On the next page, we expand on the concept of protocols which allow users to specify what collection they want to use for a certain protocol (e.g. use my collection 10 for the Follow Protocol).

**Manager**

The manager is similar to the badges interface. They are granted admin privileges to update certain things about the map.This is handled by **managerTImeline** and the **canUpdateManager** permission.

Maps also have the option to **inheritManagerTimelineFrom** a specific collection. This emans that the manager of the collection specified will be used instead of the **managerTimeline** field.

**Genesis Conditions**

Protocols may have expected genesis conditions or additional checks to be correctly implemented. There are no checks on-chain for genesis conditions but these can be handled by you.

**Map Type**

There are really four different map types.

-   Manager only means only the manager can update values
-   Collection ID means map key smust be numeric and only owners of badge ID N from the collection ID specified can update key = N.
-   Creator only means keys are address-based. You can only update the value for your address. This uses mapped BitBadges addresses.
-   First come, first serve means that map slots are open but once claimed, they can not be overwritten unless unset by the user who claimed the slot.

```typescript
export interface iMapUpdateCriteria<T extends NumberType> {
    managerOnly: boolean;
    collectionId: T;
    creatorOnly: boolean;
    firstComeFirstServe: boolean;
}
```

**Permissions / Expected Values**

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

**Map Metadata**

Map metadata follows the same interfaces as badges and address lists.

```typescript
export interface iMapMetadataTimeline<T extends NumberType> {
    timelineTimes: iUintRange<T>[];
    metadata: iCollectionMetadata;
}
```

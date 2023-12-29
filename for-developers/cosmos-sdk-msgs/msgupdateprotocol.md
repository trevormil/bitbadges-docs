# MsgUpdateProtocol

MsgUpdateProtocol updates the protocol with the matching name. The **uri** and **customData** can be used to provide additional information.&#x20;

If **isFrozen**, the details of the protocol are permanent and can never be updated.

```typescript
export interface MsgUpdateProtocol {
  creator: string,
  name: string,
  uri: string,
  customData: string,
  isFrozen: boolean,
}
```

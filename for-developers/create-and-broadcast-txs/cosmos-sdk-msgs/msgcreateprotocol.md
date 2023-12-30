# MsgCreateProtocol

MsgCreateProtocol simply creates a protocol. It is identified by its **name**, which must be unique from any previously created protocol.

The **uri** and **customData** can be used to provide additional information.&#x20;

If **isFrozen**, the details of the protocol are permanent and can never be updated.

```typescript
export interface MsgCreateProtocol {
  creator: string,
  name: string,
  uri: string,
  customData: string,
  isFrozen: boolean,
}
```

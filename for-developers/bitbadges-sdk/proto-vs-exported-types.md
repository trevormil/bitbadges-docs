# Proto vs Exported Types

There are two different kinds of types exported by the SDK.&#x20;

**Proto Types**

The proto (Protocol Buffer) types are the auto-generated types used by the blockchain. **These are typically only used for generating transactions to be broadcasted (see** [**here**](../create-and-broadcast-txs/)**).**  They are exported via the following

```typescript
import { proto } from "bitbadgesjs-sdk";
const MsgCreateProtocol = proto.protocols.MsgCreateProtocol;
```

If you ever end up with something like the following, this is also the proto definition. We recommend using the proto.abc.xyz method to avoid confusion.

```typescript
import { MsgUpdateCollection } from "bitbadgesjs-sdk/dist/proto/badges";
```

**Created Types**

We have exported lots of manually created types as well. These are typically what you will use in most of your functions. All of the SDK helper functions expect these types. They all come with a conversion function between the different NumberTypes as well (see [here](common-snippets/numbertype-conversions.md)).

```typescript
import { Balance } from "bitbadgesjs-sdk";
```

```typescript
export interface Balance<T extends NumberType> {
  amount: T;
  badgeIds: UintRange<T>[]
  ownershipTimes: UintRange<T>[]
}
```

**Duplicates**

Some might have duplicates between the two types. It is important to note the difference between the two.

```typescript
import { Balance } from "bitbadgesjs-sdk/dist/proto/balances";
import { Balance } from "bitbadgesjs-sdk";
```

# SDK Types

### Custom Types

All types used in the SDK are exported via two ways.&#x20;

**Classes**

The first is via a JavaScript class. These will always begin with a capital letter. This class will always have the core functions below. Other specific functions may also be implemented.

```typescript
export declare class CustomTypeClass<T extends CustomType<T>> implements CustomType<T> {
    toJson(): JsonObject;
    toJsonString(): string;
    equals<U extends CustomType<U>>(other: CustomType<U> | null | undefined, normalizeNumberTypes?: boolean | undefined): boolean;
    clone(): T;
    getNumberFieldNames(): string[]; //Used behind the scenes
    convert<U extends NumberType>(_convertFunction?: (val: NumberType) => U): CustomType<any>;
}
```

The .convert() function is especially useful when dealing with different NumberTypes (bigint -> string).

```typescript
import { Balance, Numberify } 

const balance = new Balance<bigint>({
    amount: 1n,
    badgeIds: [{ start: 1n, end: 100n }],
    ownershipTimes: [{ start: 1n, end: 100n }]
})
const convertedBalance = balance.convert(Numberify); //1, 100 instead of 1n, 100n
```

**Interfaces**

The second is a JavaScript interface. This is the same as the class version minus all functions (just the core JSON object).

```typescript
export interface iBalance<T extends NumberType> {
    amount: T;
    badgeIds: iUintRange<T>[];
    ownershipTimes: iUintRange<T>[];
}
```

**Which one to use?**

Many functions support both; however, you may have to convert between them occasionally for compatibility. We recommend using the classes, but we recognize that many developers prefer the interfaces.&#x20;

### Typed Arrays

Some types also have a typed array exported as well. Similar to a Uint8Array in Javascript, these have all the features of traditional arrays. Thus, you can use .find, .map, .filter(), etc. Plus, additional functions will be available on them (e.g. array.addBalances for the BalanceArray type).&#x20;

```typescript
//Option 1
const balances = new BalanceArray()
balances.push(...)

//Option 2
const balances = BalanceArray.From([{ ... })

balances.addBalances([{ ... }]); //adds balances in-place
```

### **Proto Types**

The blockchain behind the scenes uses the Protocol Buffer type language. Within the SDK, we auto-generate all these proto types for you, but these are typically not the ones you should use in development (only when broadcasting transactions (see [here](../bitbadges-blockchain/create-and-broadcast-txs/))).&#x20;

Some might have duplicates between the two types.

```typescript
import { Balance } from "bitbadgesjs-sdk/dist/proto/balances";
import { Balance } from "bitbadgesjs-sdk";
```

The Proto types are exported via the following

```typescript
import { proto } from "bitbadgesjs-sdk";
const MsgCreateProtocol = proto.protocols.MsgCreateProtocol;
```

If you ever end up with something like the following, this is also the proto definition. We recommend using the proto.abc.xyz method to avoid confusion.

```typescript
import { MsgUpdateCollection } from "bitbadgesjs-sdk/dist/proto/badges";
```

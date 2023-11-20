# NumberType Conversions

A problem with creating a JavaScript SDK for a Cosmos SDK based blockchain is that JavaScript's number primitive cannot natively handle numbers > Number.MAX\_SAFE\_INTEGER, but the blockchain allows amounts greater than that.

To combat this, we have made all TypeScript types in the SDK generic via a NumberType interface.&#x20;

```typescript
export type NumberType = bigint | number | string | boolean;
```

Types that you will find in the SDK that are number-based will all be generically typed, so that you can use any of the above NumberTypes, according to your preferences.&#x20;

It is recommended that you use bigint and/or stringified because these can represent all possible numbers and do not lose precision. Also, note that for almost all SDK functions, we only take bigints.&#x20;

For example, the following will represent a BadgeMetadata type where all numbers are stringified (i.e. "100" or "123").

```typescript
const stringifiedMetadata: BadgeMetadata<string> = { uri: ... };
```

**Converting Between NumberTypes**

To convert between different number types, all types come with a converter function. In our case, it would be **convertBadgeMetadata**. This allows you to convert all the stringified numbers to another format (such as JS number or JS bigint). To convert, you can simply do the following:

```typescript
import { BigIntify, BadgeMetadata, JSPrimitiveNumberType, NumberType, convertBadgeMetadata } from "bitbadgesjs-proto";

const stringifiedMetadata: BadgeMetadata<string> = { uri: ... };
const bigIntifiedMetadata = convertBadgeMetadata(stringifiedMetadata, BigIntify)
```

We export the following types and converter functions for your convenience from bitbadgesjs-proto.

```typescript
export type NumberType = bigint | number | string | boolean;
export type JSPrimitiveNumberType = string | number | boolean;

export const BigIntify = (item: NumberType) => numberify(item, StringNumberStorageOptions.BigInt) as bigint;
export const Stringify = (item: NumberType) => numberify(item, StringNumberStorageOptions.String) as string;
export const Numberify = (item: NumberType) => numberify(item, StringNumberStorageOptions.Number) as number;
export const NumberifyIfPossible = (item: NumberType) => numberify(item, StringNumberStorageOptions.NumberIfPossible) as number | string;
```

**Example Application**

In our API, JS bigints cannot be natively sent over HTTP. So, we use the following execution flow:

1. Before sending to the client, stringify everything before sending over HTTP
2. The client can use the converter functions to coonvert all types to their preferred method

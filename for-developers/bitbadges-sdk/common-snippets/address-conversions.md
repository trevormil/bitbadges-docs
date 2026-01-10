# Address Conversions

Documentation Link: [Here](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs) -> Address Utils

> **Note:** As of v23, BitBadges only supports Cosmos-based 'bb' prefixed bech32 addresses. The `convertToBitBadgesAddress()` function is now essentially a pass-through validation function - it validates that the input is a valid bb-prefixed address and returns it unchanged. It is still exported for compatibility but is redundant since both input and output are bb-prefixed addresses. You can also use the `isAddressValid()` function to check if an address is a valid BitBadges address.

```ts
import { convertToBitBadgesAddress } from 'bitbadgesjs-sdk';

let address = convertToBitBadgesAddress(
    'bb1z3t55m0l9h0eupuz3dp5t5cypyv674jj7mz2jw'
    // Previously: '0x....'
);
if (!!address) {
    // address is valid
}
```

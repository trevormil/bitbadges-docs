# Address Conversions

Documentation Link: [Here](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs) -> Address Utils

## Converting Between Ethereum and Cosmos Addresses

BitBadges supports both Ethereum addresses (0x-prefixed hex) and Cosmos addresses (bb-prefixed bech32). You can convert between these formats using the address converter functions.

```ts
import { convertToBitBadgesAddress, convertToEthAddress } from "bitbadgesjs-sdk"

// Convert Ethereum address to BitBadges address
let bitbadgesAddress = convertToBitBadgesAddress("0x14574a6DFF2Ddf9e07828b4345d3040919AF5652")
// "bb1z3t55m0l9h0eupuz3dp5t5cypyv674jj7mz2jw"

// Convert BitBadges address to Ethereum address
let ethAddress = convertToEthAddress("bb1z3t55m0l9h0eupuz3dp5t5cypyv674jj7mz2jw")
// "0x14574a6DFF2Ddf9e07828b4345d3040919AF5652"
```

## BitBadges Address Validation

You can also use the `convertToBitBadgesAddress()` function to validate BitBadges addresses, or use `isAddressValid()` to check if an address is valid.

```ts
import { convertToBitBadgesAddress, isAddressValid } from 'bitbadgesjs-sdk';

let address = convertToBitBadgesAddress('bb1z3t55m0l9h0eupuz3dp5t5cypyv674jj7mz2jw');
if (!!address) {
    // address is valid
}

// Or use isAddressValid
if (isAddressValid('bb1z3t55m0l9h0eupuz3dp5t5cypyv674jj7mz2jw')) {
    // address is valid
}
```

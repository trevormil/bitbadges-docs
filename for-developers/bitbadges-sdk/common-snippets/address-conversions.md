# Address Conversions

Documentation Link: [Here](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs) -> Address Utils

```ts
import { convertToCosmosAddress, convertToBtcAddress, convertToEthAddress } from "bitbadgesjs-sdk"

let address = convertToCosmosAddress("0x14574a6DFF2Ddf9e07828b4345d3040919AF5652")
// "cosmos1z3t55m0l9h0eupuz3dp5t5cypyv674jj7mz2jw"

let address = convertToEthAddress("cosmos1z3t55m0l9h0eupuz3dp5t5cypyv674jj7mz2jw")
// "0x14574a6DFF2Ddf9e07828b4345d3040919AF5652"
```

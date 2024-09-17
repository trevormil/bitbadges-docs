# Address Conversions

Documentation Link: [Here](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs) -> Address Utils

```ts
import {
    convertToBitBadgesAddress,
    convertToBtcAddress,
    convertToEthAddress,
} from 'bitbadgesjs-sdk';

let address = convertToBitBadgesAddress(
    '0xAF79152AC5dF276D9A8e1E2E22822f9713474902'
);
// "bb14au322k9munkmx5wrchz9q30juf5wjgzrvkhc4"

let address = convertToEthAddress('bb14au322k9munkmx5wrchz9q30juf5wjgzrvkhc4');
// "0xAF79152AC5dF276D9A8e1E2E22822f9713474902"
```

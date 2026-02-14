# 👤 Accounts

This page gives you an overview of BitBadges accounts and how addresses from different blockchains are supported.

## How is BitBadges able to support addresses from different blockchains?

To enable interoperability between different blockchains, each individual L1 blockchain has its native addresses mapped to an equivalent Cosmos address and an account ID number. An account ID number is assigned to the address when it interacts with the BitBadges blockchain or is sent BADGE for the first time.

As an example, the Ethereum zero address `0x0000000000000000000000000000000000000000` maps to the Cosmos address `bb1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a` and will be assigned an account ID number upon interaction with the BitBadges blockchain.

We typically refer to the native address as simply **address** and the corresponding Cosmos address as **bitbadgesAddress**. For Cosmos-native addresses, **address** will be the same as **bitbadgesAddress**.

## Which address should I use (native or mapped Cosmos one)? How to convert?

For user experience, you should always display the user's native address. Just note, certain behind the scenes state will be using the mapped Cosmos address.

```typescript
import { ethToCosmos, cosmosToEth } from 'bitbadgesjs-address-converter';

const bitbadgesAddress = ethToCosmos(address);
const ethAddress = cosmosToEth(bitbadgesAddress);
```







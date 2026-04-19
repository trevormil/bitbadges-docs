# Overview

The BitBadges SDK is a bundle of TypeScript libraries that provide all the tools and functions needed for you to build your own frontend or interact with the BitBadges API, blockchain, and indexer.

GitHub: [https://github.com/bitbadges/bitbadgesjs](https://github.com/bitbadges/bitbadgesjs)

Full Documentation: [https://bitbadges.github.io/bitbadgesjs/](https://bitbadges.github.io/bitbadgesjs/)

```
npm install bitbadges
```

This library provides miscellaneous functionality to help you interact with BitBadges, such as types, API routes, managing metadata requests, logic with ID ranges and balances, etc.

> **Building a frontend?** Start with the [React & Next.js Quickstart](react-quickstart.md) — it walks through install → connect wallet → query a collection → sign and broadcast in one page.

## Address Conversion

The SDK includes address conversion utilities to convert between Ethereum addresses (0x-prefixed) and BitBadges addresses (bb-prefixed). See [Address Conversions](common-snippets/address-conversions.md) for more details.

```typescript
import { convertToBitBadgesAddress, convertToEthAddress } from 'bitbadges';

const bitbadgesAddress = convertToBitBadgesAddress(address);
const ethAddress = convertToEthAddress(bitbadgesAddress);
```

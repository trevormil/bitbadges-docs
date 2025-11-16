# 📚 Overview

This directory contains comprehensive developer documentation for the BitBadges blockchain's `x/gamm` module.

This module was forked from Osmosis's `x/gamm` module with several key modifications to support BitBadges tokens and enhance compatibility.

## Key Differences from Osmosis

### 1. Interface Revamps

-   Removed `smoothWeightChangeParams` and other unused parameters
-   Updated certain type definitions for better compatibility with our codebase
-   Streamlined interfaces for improved performance
-   Remove unneeded logic like stableswap pools and governance proposal handling
-   Removed future pool governor functionality
-   Removed pool creation fee requirements

### 2. Badge Token Integration

The main difference is in the badge token handling system. With every attempted transfer of BitBadges tokens, the system:

-   **Wrapping Conversion**: Uses `cosmosCoinWrapperPaths` defined by the collection to abstract badge tokens to `x/bank` denominations (using the `path.balances` array) at a set conversion rate
-   **Mint/Burns**: Behind the scenes, `x/badges` denomination is used before / after each swap and to/from a pool if the asset is a BitBadges token.
-   **Pool Compatibility**: Ensures seamless integration with existing pool infrastructure
-   **Automatic Conversion**: Handles badge token transfers to/from pools automatically

There actually is not any minting / burning behind the scenes. For implementation purpsoes, the pool treats it as a "ghost denom": badgeslp:collectionID:denom, but it is really backed by core x/badges tokens.

#### Conversion Example

Here's how the badge token conversion works:

**Badge Token**: `badgeslp:21:utoken`

-   Collection ID: `21`
-   Base Denom: `utoken`
-   Wrapper Path Balances Conversion Rate: `[{ amount: 1n, tokenIds: [{ start: 1n, end: 1n }], ownershipTimes: UintRangeArray.FullRanges() }]`

**Conversion Process**:

```
1 badgeslp:21:utoken = [{ amount: 1n, tokenIds: [{ start: 1n, end: 1n }], ownershipTimes: UintRangeArray.FullRanges() }]

2 badgeslp:21:utoken = [{ amount: 2n, tokenIds: [{ start: 1n, end: 1n }], ownershipTimes: UintRangeArray.FullRanges() }]
```

### 3. Transferability Requirements and Compliance

Even though badge tokens are converted to pool-compatible denominations for liquidity pool operations, they are still subject to the same transferability requirements as any other badge token transfer via MsgTransferTokens. This means that all badge token transfers to/from pools must satisfy the collection's approval criteria, including user-level and collection-level approvals.

This design enables powerful compliance features:

-   **User-Gated Pools**: Restrict who can join or exit pools based on approval criteria
-   **Rate Limiting**: Implement daily/weekly withdrawal limits for pool exits
-   **KYC/AML Integration**: Require identity verification before allowing pool participation
-   **Geographic Restrictions**: Control pool access based on user addresses or other criteria
-   **Time-Based Controls**: Restrict pool operations to specific time windows
-   **Custom Compliance Logic**: Implement any approval criteria that can be expressed through the badge approval system

Behind the scenes, pool operations involving badge tokens are treated as transfers initiated by the user but approved by the pool address itself.

## Table of Contents

1. [Introduction](broken-reference/) - Overview and key concepts
2. [Messages](messages/) - Transaction messages and handlers

## Message Reference

### Core Operations

-   [MsgCreateBalancerPool](messages/msg-create-balancer-pool.md) - Create new balancer pool
-   [MsgJoinPool](messages/msg-join-pool.md) - Join existing pool with liquidity
-   [MsgSwapExactAmountIn](messages/msg-swap-exact-amount-in.md) - Swap exact amount of tokens in
-   [MsgExitPool](messages/msg-exit-pool.md) - Exit pool and receive tokens

## Query Reference

For all GAMM queries, please refer to the [BitBadges LCD API](https://lcd.bitbadges.io/).

The LCD provides comprehensive query endpoints for:

-   Pool information and statistics
-   Trading data and spot prices
-   Module parameters
-   And more

All queries follow the standard Cosmos SDK query patterns and can be accessed via REST API or gRPC.

## Quick Links

-   [BitBadges Chain Repository](https://github.com/bitbadges/bitbadgeschain)
-   [BitBadges Documentation](https://docs.bitbadges.io)
-   [Proto Definitions](https://github.com/bitbadges/bitbadgeschain/tree/master/proto/gamm)

## Documentation Style

This documentation follows the [Cosmos SDK module documentation standards](https://docs.cosmos.network/main/building-modules/README) and is designed for developers building on or integrating with the BitBadges blockchain.

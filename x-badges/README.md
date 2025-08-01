# 📚 Overview

This directory contains comprehensive developer documentation for the BitBadges blockchain's `x/badges` module.

This section is a knowledge dump for how badges operate behind the scenes. For most use cases, you will not care about any of this as it will be handled for you via the site. And if you are self-implementing a badge-gated service, you can just fetch badge balances and metadata from the API without worrying about the underlying details.

```typescript
const res = await BitBadgesApi.getBadgeBalanceByAddress(collectionId, address, {
    ...options,
});
console.log(res);

const res = await BitBadgesApi.getBadgeMetadata(1, 5);
```

## Table of Contents

1. [Concepts](02-concepts.md) - Core data structures and business logic
2. [Messages](messages/) - Transaction messages and handlers
3. [Queries](queries/) - Query types and endpoints
4. [Events](events.md) - Event emissions and tracking
5. [Examples](examples/) - Common usage patterns and building blocks

## Message Reference

### Collection Management

-   [MsgCreateCollection](messages/msg-create-collection.md) - Create new badge collection
-   [MsgUpdateCollection](messages/msg-update-collection.md) - Update existing collection
-   [MsgDeleteCollection](messages/msg-delete-collection.md) - Delete collection

### Badge Transfers

-   [MsgTransferBadges](messages/msg-transfer-badges.md) - Transfer badges between addresses

### User Approvals

-   [MsgUpdateUserApprovals](messages/msg-update-user-approvals.md) - Update transfer approvals

### Address Lists & Dynamic Stores

-   [MsgCreateAddressLists](messages/msg-create-address-lists.md) - Create reusable address lists
-   [MsgCreateDynamicStore](messages/msg-create-dynamic-store.md) - Create key-value store
-   [MsgUpdateDynamicStore](messages/msg-update-dynamic-store.md) - Update dynamic store properties
-   [MsgDeleteDynamicStore](messages/msg-delete-dynamic-store.md) - Delete dynamic store
-   [MsgSetDynamicStoreValue](messages/msg-set-dynamic-store-value.md) - Set address-specific store values
-   [More messages...](messages/) - See full message reference

## Query Reference

### Core Queries

-   [GetCollection](queries/get-collection.md) - Retrieve collection data
-   [GetBalance](queries/get-balance.md) - Get user badge balances
-   [GetApprovalTracker](queries/get-approval-tracker.md) - Get approval usage data
-   [GetAddressList](queries/get-address-list.md) - Retrieve address list
-   [More queries...](queries/) - See full query reference

## Quick Links

-   [BitBadges Chain Repository](https://github.com/bitbadges/bitbadgeschain)
-   [BitBadges Documentation](https://docs.bitbadges.io)
-   [Proto Definitions](https://github.com/bitbadges/bitbadgeschain/tree/master/proto/badges)

## Documentation Style

This documentation follows the [Cosmos SDK module documentation standards](https://docs.cosmos.network/main/building-modules/README) and is designed for developers building on or integrating with the BitBadges blockchain.

# Messages

This directory contains detailed documentation for all message types supported by the badges module.

## Message Categories

### Collection Management
- [MsgCreateCollection](./msg-create-collection.md) - Create new badge collection
- [MsgUpdateCollection](./msg-update-collection.md) - Update existing collection properties
- [MsgDeleteCollection](./msg-delete-collection.md) - Archive/delete collection

### Badge Transfers
- [MsgTransferBadges](./msg-transfer-badges.md) - Transfer badges between addresses with approval validation

### User Approval Management
- [MsgUpdateUserApprovals](./msg-update-user-approvals.md) - Update user transfer approval settings

### Address List Management
- [MsgCreateAddressLists](./msg-create-address-lists.md) - Create reusable address lists for access control

### Dynamic Store Management
- [MsgCreateDynamicStore](./msg-create-dynamic-store.md) - Create boolean flag stores for approval criteria

### System Administration
- [MsgUpdateParams](./msg-update-params.md) - Update module parameters via governance

## Additional Message Types

The following message types exist in the protocol but may be documented separately:

- **MsgUniversalUpdateCollection** - Legacy unified create/update interface
- **MsgUpdateDynamicStore** - Update dynamic store configuration
- **MsgDeleteDynamicStore** - Delete dynamic store
- **MsgSetDynamicStoreValue** - Set individual address values in dynamic store

## Common Patterns

All messages follow these common patterns:

1. **Message Lifecycle**: ValidateBasic() → Context Validation → Authorization → State Changes → Events
2. **Error Handling**: Descriptive errors with proper error types
3. **Event Emission**: Both standard and indexer events
4. **Gas Optimization**: Efficient operations and range-based processing

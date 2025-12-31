# Dynamic Store Challenges

Require transfer initiators to pass checks against dynamic stores. Typically, these are used in combination with smart contracts or other custom extensions.

Dynamic stores are standalone (address -> boolean) stores controlled by whoever creates them. They are stored and maintained by the creator. These are powerful for creating dynamic approval criteria with smart contracts and other custom use cases.

## How It Works

Dynamic store challenges check if the transfer initiator has a value of `true` in specified dynamic stores. The system:

1. **Checks Global Kill Switch**: First checks if the dynamic store's `globalEnabled` field is `true`. If `globalEnabled = false`, the approval fails immediately with error: "dynamic store storeId {id} is globally disabled"
2. **Checks Initiator**: If the store is globally enabled, looks up the initiator's address in the specified dynamic store
3. **Evaluates Boolean**: Returns the boolean value for the initiator (or `defaultValue` if not set)
4. **Requires All True**: All challenges must return `true` for approval
5. **Fails if Any False**: If any challenge returns `false`, transfer is denied

## Interface

```typescript
interface DynamicStoreChallenge {
    storeId: string; // Dynamic store ID to check
}
```

## Usage in Approval Criteria

```json
{
    "dynamicStoreChallenges": [
        { "storeId": "1" }, // Member store (must be true)
        { "storeId": "2" } // Subscription store (must be true)
    ]
}
```

## Global Kill Switch

Each dynamic store has a `globalEnabled` field that acts as a global kill switch. When `globalEnabled = false`, **all approvals** using that store via `DynamicStoreChallenge` will fail immediately, regardless of per-address values.

This is useful for quickly halting all approvals that depend on a specific dynamic store (e.g., if a protocol is compromised and needs to be disabled immediately).

### Behavior

- **New stores**: `globalEnabled` defaults to `true` (enabled by default)
- **Existing stores**: All existing stores are set to `globalEnabled = true` for backward compatibility
- **Disabling**: Set `globalEnabled = false` via [MsgUpdateDynamicStore](../../../x-badges/messages/msg-update-dynamic-store.md) to halt all dependent approvals
- **Re-enabling**: Set `globalEnabled = true` to restore normal operation

### Usage Example

```json
// Disable all approvals using store "1"
{
    "creator": "bb1...",
    "storeId": "1",
    "defaultValue": true,  // Must pass current value
    "globalEnabled": false  // Disable kill switch
}

// Re-enable when ready
{
    "creator": "bb1...",
    "storeId": "1",
    "defaultValue": true,
    "globalEnabled": true  // Re-enable
}
```

## Managing Dynamic Stores

### Creating Stores

Use [MsgCreateDynamicStore](../../../x-badges/messages/msg-create-dynamic-store.md) to create new dynamic stores with a default boolean value. New stores are created with `globalEnabled = true` by default.

### Updating Stores

Use [MsgUpdateDynamicStore](../../../x-badges/messages/msg-update-dynamic-store.md) to update the store's `defaultValue` and `globalEnabled` fields.

### Setting Values

Use [MsgSetDynamicStoreValue](../../../x-badges/messages/msg-set-dynamic-store-value.md) to set boolean values for specific addresses.

### Querying Values

Use [GetDynamicStoreValue](../../../x-badges/queries/get-dynamic-store-value.md) to check current values for addresses.

Use [GetDynamicStore](../../../x-badges/queries/get-dynamic-store.md) to retrieve the store's configuration, including `globalEnabled` status.

## Alternatives

For fully off-chain solutions, consider:

* [Merkle Challenges](merkle-challenges.md) to save gas costs
* [ETH Signature Challenges](eth-signature-challenges.md) for direct authorization

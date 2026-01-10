# Dynamic Store Challenges

Require specified parties (initiator, sender, recipient, or a hardcoded address) to pass checks against dynamic stores. Typically, these are used in combination with smart contracts or other custom extensions.

Dynamic stores are standalone (address -> boolean) stores controlled by whoever creates them. They are stored and maintained by the creator. These are powerful for creating dynamic approval criteria with smart contracts and other custom use cases liek global kill switches.

## How It Works

Dynamic store challenges check if a specified party has a value of `true` in specified dynamic stores. The system:

1. **Checks Global Kill Switch**: First checks if the dynamic store's `globalEnabled` field is `true`. If `globalEnabled = false`, the approval fails immediately with error: "dynamic store storeId {id} is globally disabled"
2. **Determines Check Party**: Determines which address to check based on the `ownershipCheckParty` field (defaults to "initiator" if not specified)
3. **Looks Up Address**: If the store is globally enabled, looks up the specified party's address in the dynamic store
4. **Evaluates Boolean**: Returns the boolean value for that address (or `defaultValue` if not set)
5. **Requires All True**: All challenges must return `true` for approval
6. **Fails if Any False**: If any challenge returns `false`, transfer is denied

## Interface

```typescript
interface DynamicStoreChallenge {
    storeId: string; // Dynamic store ID to check
    ownershipCheckParty?: string; // Which party to check: "initiator", "sender", "recipient", or a hardcoded bb1 address (default: "initiator")
}
```

## Usage in Approval Criteria

```json
{
    "dynamicStoreChallenges": [
        { "storeId": "1", "ownershipCheckParty": "initiator" }, // Member store (must be true for initiator, default)
        { "storeId": "2", "ownershipCheckParty": "sender" } // Subscription store (must be true for sender)
    ]
}
```

## Field Descriptions

### storeId

-   **Type**: `string`
-   **Description**: The ID of the dynamic store to check
-   **Required**: Yes
-   **Example**: `"1"` for store ID 1

### ownershipCheckParty

-   **Type**: `string` (optional)
-   **Description**: Specifies which party of the transfer to check the dynamic store value for
-   **Options**:
    -   `"initiator"` (default): Check the dynamic store value for the address that initiated the transfer
    -   `"sender"`: Check the dynamic store value for the address sending the tokens
    -   `"recipient"`: Check the dynamic store value for the address receiving the tokens
    -   Hardcoded bb1 address (e.g., `"bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls"`): Check the dynamic store value for a specific address, regardless of transfer parties
-   **Default**: `"initiator"` (if empty or not specified)
-   **Example**: `"sender"` to require the sender to have `true` in the dynamic store before allowing the transfer
-   **Hardcoded Address Example**: `"bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls"` to require a specific address to have `true` in the store (useful for multi-sig or contract-based checks)

## Global Kill Switch

Each dynamic store has a `globalEnabled` field that acts as a global kill switch. When `globalEnabled = false`, **all approvals** using that store via `DynamicStoreChallenge` will fail immediately, regardless of per-address values.

This is useful for quickly halting all approvals that depend on a specific dynamic store (e.g., if a protocol is compromised and needs to be disabled immediately).

### Behavior

-   **New stores**: `globalEnabled` defaults to `true` (enabled by default)
-   **Existing stores**: All existing stores are set to `globalEnabled = true` for backward compatibility
-   **Disabling**: Set `globalEnabled = false` via [MsgUpdateDynamicStore](../../../x-badges/messages/msg-update-dynamic-store.md) to halt all dependent approvals
-   **Re-enabling**: Set `globalEnabled = true` to restore normal operation

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

Use [MsgUpdateDynamicStore](../../../x-badges/messages/msg-update-dynamic-store.md) to update the store's `defaultValue`, `globalEnabled`, `uri`, and `customData` fields. The `uri` and `customData` fields allow storing additional metadata and arbitrary data associated with the store.

### Setting Values

Use [MsgSetDynamicStoreValue](../../../x-badges/messages/msg-set-dynamic-store-value.md) to set boolean values for specific addresses.

### Querying Values

Use [GetDynamicStoreValue](../../../x-badges/queries/get-dynamic-store-value.md) to check current values for addresses.

Use [GetDynamicStore](../../../x-badges/queries/get-dynamic-store.md) to retrieve the store's configuration, including `globalEnabled` status, `uri`, and `customData` fields.

## Alternatives

For fully off-chain solutions, consider:

-   [Merkle Challenges](merkle-challenges.md) to save gas costs
-   [ETH Signature Challenges](eth-signature-challenges.md) for direct authorization

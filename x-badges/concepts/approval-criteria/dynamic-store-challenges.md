# Dynamic Store Challenges

Require transfer initiators to pass checks against dynamic stores. Typically, these are used in combination with smart contracts or other custom extensions.

Dynamic stores are standalone (address -> boolean) stores controlled by whoever creates them. They are stored and maintained by the creator. These are powerful for creating dynamic approval criteria with smart contracts and other custom use cases.

## How It Works

Dynamic store challenges check if the transfer initiator has a value of `true` in specified dynamic stores. The system:

1. **Checks Initiator**: Looks up the initiator's address in the specified dynamic store
2. **Evaluates Boolean**: Returns the boolean value for the initiator
3. **Requires All True**: All challenges must return `true` for approval
4. **Fails if Any False**: If any challenge returns `false`, transfer is denied

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

## Managing Dynamic Stores

### Creating Stores

Use [MsgCreateDynamicStore](../../messages/msg-create-dynamic-store.md) to create new dynamic stores with a default boolean value.

### Setting Values

Use [MsgSetDynamicStoreValue](../../messages/msg-set-dynamic-store-value.md) to set boolean values for specific addresses.

### Querying Values

Use [GetDynamicStoreValue](../../queries/get-dynamic-store-value.md) to check current values for addresses.

## Alternatives

For fully off-chain solutions, consider:

-   [Merkle Challenges](merkle-challenges.md) to save gas costs
-   [ETH Signature Challenges](eth-signature-challenges.md) for direct authorization

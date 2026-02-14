# Coin Transfers

Automatic token transfers to be executed on every approval use. These are triggered every time this approval is used. This is useful for payments, payouts, swaps, and more.

Couple notes:

-   This forfeits auto-scan mode for the approval
-   Subject to allowed denominations set by the module parameters
-   Can be used with x/bank denominations or BitBadges alias denominations

## Interface

```typescript
interface iCoinTransfer<T extends NumberType> {
    to: string; // Recipient BitBadges address
    coins: iCosmosCoin<T>[];

    overrideFromWithApproverAddress: boolean; // By default (false), this is the initiator address. In the case of a collection approval, approver address is the mint escrow address.
    overrideToWithInitiator: boolean; // By default (false), this is the to address specified
}

interface iCosmosCoin<T extends NumberType> {
    amount: T;
    denom: string; // Any Cosmos SDK denomination (e.g., "ubadge", "uatom", "uosmo")
}
```

## Mint Escrow Address

The Mint Escrow Address (_mintEscrowAddress_) is a special reserved address generated from the collection ID that holds Cosmos native funds on behalf of the "Mint" address for a specific collection. This address has no known private key and is not controlled by anyone. The only way to get funds out is via collection approvals from the Mint address.

For collection approvals with `overrideFromWithApproverAddress: true`, the approver address is this special mint escrow address.

### Generation

```typescript
const mintEscrowAddress = generateAlias(
    'tokenization',
    getAliasDerivationKeysForCollection(collectionId)
);
```

### Properties

-   Longer than normal addresses
-   No private key (cannot be controlled by users)
-   Can receive Cosmos-native tokens
-   Only collection approvals can trigger transfers from it
-   Holds Cosmos native tokens (like "ubadge" tokens) associated with the Mint address for a specific collection

### Auto-Escrow During Collection Creation

The `MsgCreateCollection` interface includes a `mintEscrowCoinsToTransfer` field of type `repeated cosmos.base.v1beta1.Coin` that allows you to automatically escrow native coins to the Mint Escrow Address during collection creation.

**Benefits:**

-   **Unknown collection ID** - Escrow coins before knowing the final collection ID
-   **Automatic transfer** - Coins are automatically transferred to the generated Mint Escrow Address
-   **Collection initialization** - Funds are available immediately when the collection is created
-   **Single transaction** - Combine collection creation and coin escrow in one operation

**Usage:**

```typescript
const msgCreateCollection: MsgCreateCollection = {
    creator: 'cosmos1...',
    collectionId: '0',
    mintEscrowCoinsToTransfer: [
        {
            denom: 'ubadge',
            amount: '1000000',
        },
    ],
    // ... other collection fields
};
```

This field is particularly useful when you need to fund the Mint Escrow Address but don't know the collection ID beforehand, since the escrow address is derived from the collection ID itself. Thus, it can be done all in one transaction.

## Example

```json
[
    {
        "to": "bb1...",
        "coins": [{ "amount": "1000000000", "denom": "ubadge" }],
        "overrideFromWithApproverAddress": false,
        "overrideToWithInitiator": false
    }
]
```

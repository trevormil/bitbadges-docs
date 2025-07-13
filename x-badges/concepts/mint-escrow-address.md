# Mint Escrow Address

The Mint Escrow Address (_mintEscrowAddress_) is a special reserved address generated from the collection ID that holds Cosmos native funds on behalf of the "Mint" address for a specific collection. This address has no known private key and is not controlled by anyone. The only way to get funds out is via collection approvals from the Mint address.

## Key Concepts

### Address Generation

-   **Collection-specific** - Uniquely generated from each collection ID
-   **No private key** - Not controlled by any individual or entity
-   **Reserved address** - Cannot be used as a regular user address
-   **Longer format** - May be longer than normal BitBadges addresses

### Relationship to Mint Address

-   **Mint representation** - The "Mint" address is typically represented as `"Mint"` but when Cosmos-native funds are involved, we use the mintEscrowAddress instead

## Functionality

### Cosmos Native Fund Storage

The Mint Escrow Address can hold Cosmos native tokens (like "ubadge" tokens) that are associated with the Mint address for a specific collection.

See the coinTransfers section for more details. This is the only way to get funds out of the Mint Escrow Address.

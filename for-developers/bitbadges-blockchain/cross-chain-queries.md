# Cross-Chain Token Ownership Queries (ICQ)

BitBadges supports Interchain Queries (ICQ) that allow other Cosmos chains to verify token ownership without transferring tokens. This enables cross-chain gating, proof-of-ownership, and other interoperability use cases.

## Overview

The ICQ system provides two main query types:

1. **OwnershipQuery**: Streamlined lookup for a single (tokenId, ownershipTime) combination - returns the exact balance amount
2. **FullBalanceQuery**: Returns the complete UserBalanceStore including all balances, approvals, and permissions

Tokens remain in the BitBadges "silo" - for value transfer across chains, use the wrapping mechanism to convert to standard IBC-transferable tokens (BADGE, ATOM, etc).

## Packet Types

### OwnershipQueryPacket

Query the balance amount for a specific token ID and ownership time.

| Field | Type | Description |
|-------|------|-------------|
| `query_id` | string | Unique identifier for correlation |
| `address` | string | Address to check (bech32 or 0x hex) |
| `collection_id` | string | Collection ID to query |
| `token_id` | string | Single token ID to check (uint as string) |
| `ownership_time` | string | Single ownership time to check (uint as string, typically ms timestamp) |

### OwnershipQueryResponsePacket

Response with ownership verification result.

| Field | Type | Description |
|-------|------|-------------|
| `query_id` | string | Correlation ID from request |
| `owns_tokens` | bool | True if total_amount > 0 |
| `total_amount` | Uint | Exact balance amount for the specified (tokenId, ownershipTime) |
| `balance_proof` | bytes | Optional IAVL Merkle proof |
| `proof_height` | uint64 | Block height at which response was generated |
| `error` | string | Error message (empty on success) |

### FullBalanceQueryPacket

Query the complete balance store for a user (balances, approvals, permissions).

| Field | Type | Description |
|-------|------|-------------|
| `query_id` | string | Unique identifier for correlation |
| `address` | string | Address to check (bech32 or 0x hex) |
| `collection_id` | string | Collection ID to query |

### FullBalanceQueryResponsePacket

Response with the complete UserBalanceStore.

| Field | Type | Description |
|-------|------|-------------|
| `query_id` | string | Correlation ID from request |
| `balance_store` | bytes | Serialized UserBalanceStore (protobuf bytes) |
| `proof_height` | uint64 | Block height at which response was generated |
| `error` | string | Error message (empty on success) |

The `balance_store` contains:
- `balances` - List of Balance objects (amount, tokenIds ranges, ownershipTimes ranges)
- `outgoingApprovals` - Outgoing transfer approvals
- `incomingApprovals` - Incoming transfer approvals
- `autoApproveSelfInitiatedOutgoingTransfers` - Auto-approve setting
- `autoApproveSelfInitiatedIncomingTransfers` - Auto-approve setting
- `autoApproveAllIncomingTransfers` - Auto-approve setting
- `userPermissions` - User's permissions

### Bulk Queries

`BulkOwnershipQueryPacket` and `BulkOwnershipQueryResponsePacket` allow querying multiple addresses in a single IBC packet (max 100 queries).

## IBC Channel Setup

- **Port:** `tokenization`
- **Version:** `tokenization-1`
- **Channel Ordering:** `UNORDERED`

## Example Usage

### Simple Ownership Check (Single ID/Time)

```go
// Create ownership query packet for a single token ID and time
query := &types.OwnershipQueryPacket{
    QueryId:       "my-query-123",
    Address:       "bb1abc...",      // or "0x..." format
    CollectionId:  "5",
    TokenId:       "1",              // Single token ID
    OwnershipTime: "1609459200000",  // Single timestamp (ms)
}

// Wrap in packet data
packetData := &types.TokenizationPacketData{
    Packet: &types.TokenizationPacketData_OwnershipQuery{
        OwnershipQuery: query,
    },
}

// Send via IBC channel to BitBadges
// Response contains exact balance amount for that token/time
```

### Full Balance Store Query

```go
// Create full balance query packet
query := &types.FullBalanceQueryPacket{
    QueryId:      "my-query-456",
    Address:      "bb1abc...",
    CollectionId: "5",
}

// Wrap in packet data
packetData := &types.TokenizationPacketData{
    Packet: &types.TokenizationPacketData_FullBalanceQuery{
        FullBalanceQuery: query,
    },
}

// Send via IBC channel to BitBadges
// Response contains serialized UserBalanceStore with all data
```

## Use Cases

- **Cross-chain token gating:** Verify badge ownership before allowing access on another chain
- **DeFi collateral verification:** Prove token ownership without transferring
- **Multi-chain identity:** Use BitBadges tokens as credentials across Cosmos ecosystem
- **Governance:** Weight votes based on token holdings verified via ICQ
- **Cross-chain approval checking:** Query full balance store to check approval status

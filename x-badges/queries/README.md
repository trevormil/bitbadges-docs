# Queries

This directory contains detailed documentation for all query types supported by the badges module.

## Query Categories

### Collection Queries
- [GetCollection](./get-collection.md) - Retrieve collection data and properties

### Balance Queries
- [GetBalance](./get-balance.md) - Get user badge balances for a collection

### Address List Queries
- [GetAddressList](./get-address-list.md) - Retrieve address list information

### Approval Tracking Queries
- [GetApprovalTracker](./get-approval-tracker.md) - Get approval usage tracking data and limits
- [GetChallengeTracker](./get-challenge-tracker.md) - Get challenge completion tracking status

### Dynamic Store Queries
- [GetDynamicStore](./get-dynamic-store.md) - Get dynamic store configuration and metadata
- [GetDynamicStoreValue](./get-dynamic-store-value.md) - Get boolean value for specific address in store

### System Queries
- [Params](./params.md) - Get current module parameters and configuration

## Query Interface Types

The badges module provides queries through multiple interfaces:

### gRPC Interface
All queries are available via gRPC for programmatic access from clients and other modules.

### REST API
RESTful HTTP endpoints for web applications and external integrations.

### CLI Commands
Command-line interface for node operators and developers.

### TypeScript Client
Generated TypeScript client for web applications and Node.js services.

## Query Patterns

### Pagination Support
Large result sets support standard Cosmos SDK pagination for efficient data retrieval.

### Error Handling
Consistent error responses with descriptive messages for debugging and user feedback.

### Performance Optimization
- Direct lookups by ID for O(1) access
- Range-based queries with binary search
- Efficient indexing for fast retrieval

### Client Integration
All queries support multiple client types with consistent interfaces and response formats.

For implementation examples and integration patterns, see the main [Queries documentation](../05-queries.md).
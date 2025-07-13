# Overview

The `x/badges` module is the core module of the BitBadges blockchain, providing a comprehensive system for creating, managing, and transferring digital badges with sophisticated approval mechanisms, time-based properties, and cross-chain compatibility.

## Key Features

### Digital Badge Collections
- Create unique badge collections with auto-incrementing IDs
- Support for unlimited badge varieties within collections
- Flexible metadata management for collections and individual badges
- Timeline-based property evolution

### Advanced Transfer System
- Multi-level approval system (collection, sender, recipient)
- Complex approval criteria with Merkle challenges
- Time-based and usage-limited transfers
- Predetermined balance distribution patterns

### Timeline-Based Properties
- Nearly all collection properties can change over time
- Immutable timeline entries prevent historical manipulation
- Support for future property changes at specific block times

### Cross-Chain Compatibility
- Built-in IBC support for cross-chain transfers
- Multi-chain signature support (Ethereum, Bitcoin, Solana, Cosmos)
- EIP712 schema integration for Ethereum wallets

### Flexible Balance Types
- **Standard**: On-chain balance tracking
- **Off-Chain Indexed**: Centralized but verifiable
- **Off-Chain Non-Indexed**: Fully centralized
- **Non-Public**: Private balance information

### Granular Permission System
- Fine-grained control over collection management
- First-match permission policy
- Permanent permission decisions
- Time-bound permission scopes

## Architecture Overview

```
x/badges Module
├── Collections (Badge Definitions)
├── Balances (Ownership Tracking)
├── Approvals (Transfer Rules)
├── Address Lists (Reusable Address Groups)
├── Dynamic Stores (Custom Key-Value Storage)
└── Approval Trackers (Usage Monitoring)
```

## Core Components

### Collections
The fundamental unit representing a group of related badges. Each collection has:
- Unique auto-incrementing ID
- Manager (controller address)
- Metadata and standards
- Valid badge ID ranges
- Balance type configuration
- Permission settings
- Default balances for new users

### Balances
Ownership records linking users to badge amounts for specific:
- Badge ID ranges
- Time ranges (when ownership is valid)
- Amount ranges (quantity owned)

### Approvals
Three-tier approval system that all transfers must satisfy:
1. **Collection Approvals**: Set by collection manager
2. **Outgoing Approvals**: Set by badge sender
3. **Incoming Approvals**: Set by badge recipient

### Address Lists
Reusable collections of addresses for approval configurations, supporting:
- Whitelist and blacklist logic
- Address ranges and wildcards
- Reserved addresses ("Mint", "Total")

### Dynamic Stores
Generic key-value storage system for custom logic:
- Store ID + Address → Boolean value
- Creator-controlled access
- Used for complex approval criteria

## Use Cases

### Educational Credentials
- Issue certificates and diplomas as non-transferable badges
- Time-locked access to advanced courses
- Prerequisite verification through must-own badge challenges

### Corporate Recognition
- Employee achievement badges with approval workflows
- Department-specific transfer permissions
- Quarterly performance tracking with timeline properties

### Gaming Assets
- Achievement unlocks with complex criteria
- Tradeable item collections with marketplace integration
- Season-based properties using timeline management

### Event Management
- Ticket distribution with Merkle tree whitelists
- VIP access badges with temporal validity
- Transferable or non-transferable ticket types

### Governance Tokens
- Voting power representation with delegation
- Time-locked voting rights
- Multi-signature approval requirements

## Integration Points

### IBC (Inter-Blockchain Communication)
- Native support for cross-chain badge transfers
- Packet acknowledgment handling
- Timeout and error recovery mechanisms

### WASM Smart Contracts
- Custom message bindings for contract integration
- Query interfaces for contract-based applications
- Dynamic store integration for contract state

### Multi-Chain Signatures
- Ethereum EIP712 signature support
- Bitcoin and Solana JSON schema signatures
- Flexible signature verification system

## Security Features

### Approval Validation
- Version tracking prevents replay attacks
- Usage counters for limited-use approvals
- Merkle challenge verification

### Permission System
- Immutable permission decisions
- First-match policy prevents conflicts
- Time-bound permission scopes

### State Integrity
- Range validation prevents overlaps
- Timeline immutability after block time
- Comprehensive validation in message handlers

## Getting Started

To begin working with the badges module:

1. **[Understand Core Concepts](./02-concepts.md)** - Learn about collections, balances, and approvals
2. **[Explore State Management](./state.md)** - Understand how data is stored and retrieved
3. **[Review Message Types](./messages/)** - Learn about available operations
4. **[Try Examples](./examples.md)** - See common usage patterns in action

## Repository Links

- [Proto Definitions](https://github.com/bitbadges/bitbadgeschain/tree/master/proto/badges)
- [Module Implementation](https://github.com/bitbadges/bitbadgeschain/tree/master/x/badges)
- [Chain Handlers](https://github.com/bitbadges/bitbadgeschain/tree/master/chain-handlers)
- [BitBadges Typescript SDK](https://github.com/bitbadges/bitbadgesjs)

## Module Status

The badges module is currently in battle-testing phase and actively maintained. 
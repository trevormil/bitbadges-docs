# BitBadges and ERC-3643: How They Work Together

Looking to understand how BitBadges and ERC-3643 (T-REX) relate? They are not competing standards -- they operate at different layers, and you can use both together. BitBadges is a Layer 1 blockchain with EVM compatibility, meaning you can deploy ERC-3643 contracts directly on BitBadges while also taking advantage of the protocol-level token standard.

## Overview

**ERC-3643** (also known as T-REX, Token for Regulated EXchanges) is an Ethereum-based standard for compliant security tokens. It has been ratified by the ERC process and has facilitated over $32 billion in tokenized assets. It is the most widely adopted standard for institutional-grade tokenized securities, with strong backing from the financial sector.

**BitBadges** is a Cosmos-based Layer 1 blockchain with EVM compatibility via precompiles. It offers two paths for token creation:

1. **The built-in protocol-level token standard** -- compliance rules, transfer restrictions, and approval logic are configured directly through structured messages with no smart contract development required.
2. **EVM smart contracts** -- deploy any Solidity contract (including ERC-3643) on BitBadges through its EVM execution environment, with full access to Cosmos-native features like IBC.

This means BitBadges is not an alternative to ERC-3643 -- it is a chain where you can deploy ERC-3643 *and* access additional protocol-level primitives that are not available on Ethereum.

## Feature Comparison

| Feature | BitBadges Protocol Standard | ERC-3643 (deployable on BitBadges or Ethereum) |
|---------|-----------|----------|
| **Enforcement layer** | Protocol-level -- rules are enforced by the chain itself | Contract-level -- rules are enforced by Solidity smart contracts |
| **Smart contracts required** | No -- collections are configured through structured transaction messages | Yes -- each token requires deploying multiple Solidity contracts (token, identity registry, compliance module, claim topics) |
| **Deployment experience** | No-code UI, CLI template builders, or AI-assisted MCP tools | Developer-only -- requires Solidity expertise and contract deployment |
| **Multi-chain support** | Native IBC (Inter-Blockchain Communication) to all Cosmos chains | Bridge-dependent for cross-chain transfers (unless deployed on BitBadges, which has native IBC) |
| **Identity and compliance** | Built-in approval criteria with configurable checks (ownership requirements, merkle proofs, signature challenges, on-chain queries) | ONCHAINID identity framework with claim topics and trusted issuers |
| **Transfer restrictions** | Configurable per-approval rules: address allowlists, time windows, amount limits, tracker-based caps, 2FA gating, coin payment requirements | Compliance modules with modular rule contracts (country restrictions, investor limits, time locks) |
| **Permissioning** | Granular, lockable permission system -- each field can be frozen or made manager-controlled independently | Owner/agent role system with recovery mechanisms |
| **Token types supported** | Fungible tokens, NFTs, subscriptions, vaults, prediction markets, bounties, and more -- all from the same standard | Primarily equity/security tokens (fungible) |
| **Forced transfers** | Supported via admin override approvals | Supported via recovery and forced transfer functions |
| **Supply control** | Configurable mint/burn rules with lockable permissions | Mint/burn controlled by token agents |

## ERC-3643 Strengths

- **Institutional adoption** -- ERC-3643 is a ratified Ethereum standard with $32B+ in tokenized assets and adoption by major financial institutions
- **Regulatory track record** -- Purpose-built for securities compliance with established legal frameworks
- **Ecosystem maturity** -- Rich ecosystem of identity providers, compliance modules, and institutional tooling
- **ONCHAINID framework** -- Mature decentralized identity solution for KYC/AML compliance

## BitBadges Protocol Standard Strengths

- **No smart contract development** -- Creating a compliant token requires zero code; everything is configured through transaction parameters or the no-code UI
- **Protocol-level guarantees** -- Transfer rules cannot be bypassed by contract bugs or upgradeable proxy exploits because they are enforced by the chain itself
- **Broader token types** -- The same standard handles subscriptions, prediction markets, vaults, auctions, bounties, NFTs, and more -- not just equity tokens
- **AI and automation friendly** -- MCP tools and CLI template builders allow AI agents to create and manage compliant tokens programmatically
- **Cosmos ecosystem** -- Native IBC connectivity to 50+ Cosmos chains without bridges
- **Lower barrier to entry** -- No Solidity expertise, gas optimization, or contract auditing required

## Using Both Together

Because BitBadges has full EVM compatibility, you don't have to choose one approach. You can:

- **Deploy ERC-3643 contracts on BitBadges** and gain native IBC multi-chain support that isn't available on Ethereum
- **Use the protocol-level standard** for tokens that don't need the ERC-3643 contract architecture
- **Combine both** -- use ERC-3643 for regulated securities while using the protocol standard for access passes, subscriptions, or other token types in the same ecosystem
- **Migrate existing ERC-3643 deployments** to BitBadges with minimal contract changes, gaining access to Cosmos-native features

The right choice depends on your use case. If you have existing Solidity contracts and institutional tooling built around ERC-3643, deploy them on BitBadges and get IBC for free. If you want to launch compliant tokens without writing code, use the protocol standard. For complex deployments, use both.

## Further Reading

- [BitBadges Token Standard Overview](../x-tokenization/README.md)
- [Approvals and Transfer Rules](../learn/transferability.md)
- [Permissions System](../learn/permissions.md)
- [BitBadges L1 vs Other Protocols](comparing-bitbadges-to-other-protocols.md)
- [ERC-3643 Specification](https://erc3643.info/) (external)

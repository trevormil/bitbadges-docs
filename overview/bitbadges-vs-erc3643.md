# BitBadges and ERC-3643: How They Work Together

Looking to understand how BitBadges and ERC-3643 (T-REX) relate? They are not competing standards -- they operate at different layers. BitBadges' protocol-level token standard handles the compliance logic natively, and developers familiar with ERC-3643 can use it as a Solidity interface that calls into the BitBadges standard through EVM precompiles. You get the familiar ERC-3643 API backed by protocol-level enforcement.

## Overview

**ERC-3643** (also known as T-REX, Token for Regulated EXchanges) is an Ethereum-based standard for compliant security tokens. It has been ratified by the ERC process and has facilitated over $32 billion in tokenized assets. It is the most widely adopted standard for institutional-grade tokenized securities, with strong backing from the financial sector.

**BitBadges** is a Cosmos-based Layer 1 blockchain with a built-in programmable token standard. Compliance rules, transfer restrictions, and approval logic are enforced directly at the protocol level -- no smart contract deployment required. For developers coming from an EVM background, BitBadges provides EVM precompiles that let you interact with the native token standard through familiar Solidity interfaces like ERC-3643. The protocol standard does the heavy lifting; the ERC-3643 interface is just one way to call into it.

## Feature Comparison

| Feature | BitBadges Protocol Standard | ERC-3643 Interface (via precompiles on BitBadges, or natively on Ethereum) |
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

## How They Work Together

ERC-3643 and the BitBadges protocol standard are not separate systems -- ERC-3643 serves as a Solidity interface layer on top of the BitBadges standard. Through EVM precompiles, Solidity contracts can call into the native token standard using the ERC-3643 API that developers already know.

This means:

- **ERC-3643 as a familiar interface** -- Solidity developers can interact with BitBadges tokens using the ERC-3643 function signatures they already know, while the actual compliance enforcement happens at the protocol level
- **Protocol-level enforcement under the hood** -- Transfer rules, identity checks, and compliance logic are enforced by the chain itself, not by the Solidity contract. The precompile bridges the call into the native standard
- **No-code path still available** -- You don't need to use the ERC-3643 interface at all. The no-code UI, CLI template builders, and MCP tools interact with the protocol standard directly
- **Best of both worlds** -- Get the institutional familiarity and tooling compatibility of ERC-3643 with the protocol-level guarantees and broader token type support of BitBadges

## Further Reading

- [BitBadges Token Standard Overview](../x-tokenization/README.md)
- [Approvals and Transfer Rules](../learn/transferability.md)
- [Permissions System](../learn/permissions.md)
- [BitBadges L1 vs Other Protocols](comparing-bitbadges-to-other-protocols.md)
- [ERC-3643 Specification](https://erc3643.info/) (external)

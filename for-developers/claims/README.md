# BitBadges Claims

Claims are BitBadges' universal off-chain gating primitive: **Meet criteria → Receive rewards.**

Claims are composed of plugins executed in a configurable pipeline. Built-in plugins handle common patterns (codes, passwords, address whitelists, token ownership). Custom plugins let you add any logic via HTTP endpoints.

## Pages

| Page                                    | Description                                                          |
| --------------------------------------- | -------------------------------------------------------------------- |
| [Overview](overview.md)                 | How claims work, standard vs on-demand, supported plugins            |
| [Core Concepts](concepts.md)            | Claim numbers, success logic, sign-in, gating approvals, claim codes |
| [Built-In Plugins](built-in-plugins.md) | Schemas and configuration for all core + pre-built plugins           |
| [Dynamic Stores](dynamic-stores.md)     | BitBadges-hosted user lists for gating claims                        |
| [Custom Plugins](custom-plugins.md)     | Build your own plugins with HTTP endpoints                           |
| [Gating On-Chain Approvals](gating-approvals.md) | Hybrid off-chain/on-chain Merkle proof process, consistency requirements |
| [Security & Trust Assumptions](security.md) | Versioning, oracle trust, plugin risks, minimizing trust |
| [API Reference](api-reference.md)       | Complete, fetch, simulate, and verify claims programmatically        |
| [Examples](examples.md)                 | Full E2E claim configurations and completion snippets                |
| [Designing Claims](designing-claims.md) | Criteria approaches, rewards, automation, and design tips            |

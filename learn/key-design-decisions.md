# Design Decisions

### Universality

Our token standard is universal and feature-rich enough for any use case. It can be used for NFTs, fungible tokens, subscriptions, quests, credentials, real-world assets, complex regulatory compliance, or anything you can think of. The problem with existing standards is they are too limited for 95% of real-world applications. One standard to rule them all.

### No-Code / Module Approach

The BitBadges standard is no-code by default. It uses a Cosmos module approach to offer a seamless, developer-friendly API-like experience rather than complex smart contracts.

You don't need any technical knowledge to use our standard. Simply go to our site and start building! The native support is feature-rich enough that we envision that 99% of users will NEVER need to write any code, no matter how complex the use case.

This promotes reusability and battle-testing the code, rather than potential vulnerabilities introduced per token contract.

### Ever-Evolving

BitBadges is an ever-evolving standard. We are not making the same mistakes as ERC-20/721/etc. We are not stuck in the past. We are always on the bleeding edge of technology and will never be behind a trend. No technical debt accrual.

New feature idea? We will add it. It is purpose-built for the next-generation of tokenization.

### IBC Compatibility

We are Cosmos through and through. Our plan is to be the tokenization launchpad for all of Cosmos with IBC at the core of everything.

While tokens are siloed to the module, we support IBC / ICS20 in a variet of ways:

1. Any token created is custom wrappable to ICS-20/721 format
2. Tap into IBC / ICS20 denominations for payments, subscriptions, swaps, liquidity, etc.
3. Our module is IBC-enabled and core Msgs can be executed via IBC
4. One-signature aggregation of IBC transfers for multi-hop swaps, etc using our standard

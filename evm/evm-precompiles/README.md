# EVM Precompiles Documentation

> ⚠️ **Important Pre-Reading: Production Status**
> 
> **The EVM integration is not currently deployed on BitBadges mainnet.**
> 
> 1. **Development Status**: The EVM implementation is available on the `evm-poc` branch of the [bitbadgeschain repository](https://github.com/BitBadges/bitbadgeschain).
> 
> 2. **Testing Options**:
>    - You can run it locally by checking out the `evm-poc` branch
>    - Reach out to us to set up a dedicated testnet if that would be easier for your use case
>    - For a full end-to-end example, see the [counter-dapp directory](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp) in the evm-poc repository - a simple counter dApp demonstrating complete integration
>    - See the [contracts folder](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts) for examples, the precompile interface, and helper libraries
> 
> 3. **Code Status & Licensing**:
>    - The implementation is **fully code complete** and functional
>    - We are open to discussing licensing models or other collaboration ideas
>    - **Deploying EVM on BitBadges mainnet is not in our short-term plans**
> 
> Please keep this in mind when evaluating the EVM integration for your projects.

Welcome to the BitBadges Chain EVM Precompiles documentation. This documentation provides comprehensive guides for using precompiled contracts to interact with Cosmos SDK modules from Solidity smart contracts.

## Quick Links

- [Overview](overview.md) - Introduction to EVM precompiles
- [Developer Guide](developer-guide.md) - **Essential guide for app developers** - Transaction signing, address conversion, and limitations
- [Getting Started](getting-started.md) - Quick start guide
- [Architecture](architecture.md) - System architecture
- [Tokenization Precompile](tokenization-precompile/README.md) - Tokenization precompile documentation

## What are Precompiles?

Precompiles are special contract addresses that execute native Go code instead of EVM bytecode. They provide:

- **Native Performance**: Direct access to Cosmos SDK modules
- **Type Safety**: Full type conversion between Solidity and Cosmos SDK
- **Security**: Built-in validation and error handling
- **Gas Efficiency**: Optimized gas costs

## Available Precompiles

### Tokenization Precompile

**Address:** `0x0000000000000000000000000000000000001001`

Full access to the BitBadges tokenization module:
- Token transfers with approval systems
- Collection management
- Balance queries
- Approval management
- Dynamic stores
- Governance features

[Read the Tokenization Precompile Documentation →](tokenization-precompile/README.md)

## Documentation Structure

```
docs/evm-precompiles/
├── SUMMARY.md              # GitBook table of contents
├── overview.md             # Introduction to precompiles
├── developer-guide.md      # Essential guide: signing, addresses, limitations
├── getting-started.md      # Quick start guide
├── architecture.md         # System architecture
├── tokenization-precompile/
│   ├── README.md          # Tokenization precompile overview
│   ├── overview.md        # Detailed overview
│   ├── installation.md    # Setup guide
│   ├── api-reference.md   # Complete API reference
│   ├── transactions.md    # Transaction methods
│   ├── queries.md         # Query methods
│   ├── types.md           # Type definitions
│   ├── errors.md          # Error handling
│   ├── gas.md             # Gas costs
│   ├── security.md         # Security best practices
│   └── examples.md        # Code examples
├── examples/              # Tutorials and examples
├── integration-guide.md   # Integration guide
├── troubleshooting.md     # Common issues
└── faq.md                 # Frequently asked questions
```

## Getting Started

1. **Read the Overview**: Understand what precompiles are and how they work
2. **Read the Developer Guide**: **Essential reading** - Learn about transaction signing, address conversion, and limitations
3. **Follow the Getting Started Guide**: Set up your first precompile interaction
4. **Explore the Tokenization Precompile**: Learn about available methods
5. **Check Examples**: See real-world usage patterns

## Resources

- [Cosmos SDK EVM Documentation](https://docs.cosmos.network/evm/v0.5.0/documentation/overview) - Official Cosmos SDK EVM docs
- [Tokenization Module](../_docs/TOKENIZATION_MODULE_ARCHITECTURE.md) - Tokenization module architecture
- [Example Contracts](../contracts/examples/) - Solidity example contracts
- [Counter dApp Example](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/counter-dapp) - Full end-to-end counter dApp in the evm-poc repository
- [Contracts Folder](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts) - Examples, precompile interface, and helper libraries

## Contributing

Documentation improvements are welcome! Please:

1. Follow the existing documentation style
2. Include code examples where helpful
3. Link to relevant Cosmos SDK documentation
4. Keep examples up-to-date with the latest code

## Support

For questions or issues:

- Check the [FAQ](faq.md)
- Review [Troubleshooting](troubleshooting.md)
- Open an issue on GitHub

---

**Last Updated:** 2024-12-19  
**Version:** 1.0.0





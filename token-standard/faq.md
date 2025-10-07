# ❓ FAQ

### **Are smart contracts needed?**

No! All tokens are no-code with tons of functionality out of the box. One universal standard that can support any use case with no code, no smart contracts.

### **Is BitBadges an L1 blockchain or an L2?**

BitBadges is its own Layer-1 blockchain built with Cosmos SDK.

### **Why the registry architecture over unique smart contracts for every collection?**

Typical NFTs and digital tokens (ERC721, ERC20) all require their own unique smart contracts to be implemented which follow their respective interface. However, BitBadges is not built like this. BitBadges uses a single registry and the same code is reused for all collections. This is a much more secure and scalable solution.

We do this for multiple reasons:

1. Security: The same code is being reused and over time, it will become more battle-tested and more secure. This is as opposed to unique smart contracts that can often have vulnerabilities, as seen with the large amount of hacks occurring in the Ethereum ecosystem.
2. Scalability: Since duplicate code doesn't need to be deployed, this solution is much more scalable.
3. Consistency: This provides a much cleaner and more consistent interface for querying, indexing, and maintenance.

### **Are tokens ERC-721 or ERC-20 compatible?**

While our token standard takes inspiration from existing standards like ERC-721, our token standard has its own properties and architecture. However, they are a superset of these standards (offer all the features these standards + 1000x more).

Cosmos modules (like our token standard) can be called from EVM environments using the Cosmos EVM module + precompiles. This also could allow you to offer seamless compatibility with all your favorite interfaces.

### **How does BitBadges compare to other interoperability protocols?**

BitBadges is a unique L1 blockchain that simplifies multi-chain experiences. Unlike other interoperability protocols, all tokens are stored on the BitBadges chain, simplifying development. And, we support any user from any chain. No need to worry about bridging tokens or managing multiple tokens.&#x20;

BitBadges doesn't "pull" data from other chains - all relevant data is stored on the BitBadges chain, simplifying development. However, it supports users and wallets from any chain, allowing multi-chain transfers (e.g., Bitcoin users can transfer tokens to Solana users).

Note that we support Cosmos / IBC wrapping for true interoperability where you may need.

### **How does Cosmos / IBC wrapping work?**

While our token standard and all state is scoped to the module, we allow you to define "wrapper paths" to convert freely to / from x/bank (IBC) denominations. Once wrapped, you could get all the benefits of IBC + the Cosmos ecosystem with interoperability and more.

### **Does BitBadges support extending with smart contracts?**

Yes, our token standard is a Cosmos module. While we aim for no smart contracts to ever be needed, you can definitely still call in to our module from EVM and CosmWASM environments.

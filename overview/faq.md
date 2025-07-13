# ❓ FAQ

### **Badge vs $BADGE?**

Whenever we say **$BADGE**, we are referring to the BitBadges blockchain gas denomination used for paying transaction fees. All transactions on the BitBadges blockchain require $BADGE to be paid as a fee. This is not to be confused with badges / badge collections.

### **Are smart contracts needed?**

No! All badges are no-code with tons of functionality out of the box. If you need something custom, you can create a WASM or EVM (coming soon) contract to add custom functionality not already implemented.

### **Is BitBadges an L1 blockchain or an L2?**

BitBadges is its own Layer-1 blockchain built with Cosmos SDK. We also offer many off-chain services built on top of the blockchain as well.

### **Why the registry architecture over unique smart contracts for every collection?**

Typical NFTs and digital tokens (ERC721, ERC20) all require their own unique smart contracts to be implemented which follow their respective interface. However, BitBadges is not built like this. BitBadges uses a single registry and the same code is reused for all collections. This is a much more secure and scalable solution.

We do this for multiple reasons:

1. Security: The same code is being reused and over time, it will become more battle-tested and more secure. This is as opposed to unique smart contracts that can often have vulnerabilities, as seen with the large amount of hacks occurring in the Ethereum ecosystem.
2. Scalability: Since duplicate code doesn't need to be deployed, this solution is much more scalable.
3. Consistency: This provides a much cleaner and more consistent interface for querying, indexing, and maintenance.

Yes, this may sacrifice a little customizability, but we allow you to extend the interface and implement any custom logic required with smart contracts, if necessary. We believe the pros vastly (security, scalability, and ease of use) vastly outweigh the cons.

### **Are badges ERC-721 or ERC-20 compatible?**

While our token standard takes inspiration from existing standards like ERC-721, our token standard has its own properties and architecture.

### **What happens if there is convergence to a single blockchain ecosystem?**

BitBadges is uniuqely positioned at the center of any future trend. We can support any user from any chain. We can tap into any ecosystem through IBC x Cosmos. We have a CosmWASM layer. We have an EVM layer.

### **How does BitBadges compare to other interoperability protocols?**

BitBadges is a unique L1 blockchain that simplifies multi-chain experiences. Unlike other interoperability protocols, all tokens are stored on the BitBadges chain, simplifying development. And, we support any user from any chain. No need to worry about bridging tokens or managing multiple tokens.

### **How does cross-chain interoperability work?**

BitBadges doesn't "pull" data from other chains - all relevant data is stored on the BitBadges chain, simplifying development. However, it supports users and wallets from any chain, allowing multi-chain transfers (e.g., Bitcoin users can transfer badges to Solana users).

### **Does BitBadges support smart contracts?**

Yes, BitBadges supports CosmWASM contracts and EVM (coming soon) for extending functionality.

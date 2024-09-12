# ❓ FAQ

### **Badge vs $BADGE?**

Whenever we say **$BADGE**, we are referring to the BitBadges blockchain gas credit system used for paying transaction fees. This is the in-app compute credits system. All transactions on the BitBadges blockchain require $BADGE to be paid as a fee. This is not to be confused with badges / badge collections.

### **Why is there a need for BitBadges?**

The answer is simple. We believe in the potential of digital blockchain tokens, but this potential cannot be realized with the current infrastructure and technology in place today ([see here](../)). BitBadges wants to help blockchain tokens realize their potential by building this infrastructure!

### **Main use case for BitBadges?**

The main use case we envision is authentication. Almost all existing products today require some sort authentication, digital tokens, or token-gating (e.g. tiered services) in their backend infrastructure. And oftentimes, this infrastructure costs millions of dollars and utilizes thousands of hours in maintenance, especially if using a centralized service. With BitBadges, all authentication and token-gating can be **outsourced** to **greatly** reduce maintenance, overhead, and cost, as well as improving security, verifiability, availability, and much more.

### **Are there plans to support digital collectibles (tokens with value) rather than just badges?**

We want to focus on badges and leave "valuable" tokens to each respective blockchain ecosystem. We want BitBadges to be a hub for achievements, reputation, authentication, etc, not a place for buying, selling, and speculating. That is why $BADGE should be treated as an in-app credits system, not a financial asset.

### **Are smart contracts needed?**

No, we do not require smart contracts. All badges follow the same state-of-the-art interface with lots of functionality supported. Although, you can create a smart contract to add custom functionality not already implemented.

### **Is BitBadges an L1 blockchain or an L2?**

BitBadges is its own Layer-1 blockchain built with Cosmos SDK.

### **What is the difference between BitBadges (company) and BitBadges (protocol)?**

The BitBadges protocol consists of the decentralized blockchain that is maintained by the BitBadges team, but it will be ultimately governed in a decentralized manner via the $BADGE token.

BitBadges (the company) offers tools and services to interact with the protocol, such as the frontend at https://bitbadges.io, the API, the SDK, etc.

### **Why the registry architecture over unique smart contracts for every collection?**

Typical NFTs and digital tokens (ERC721, ERC20) all require their own unique smart contracts to be implemented which follow their respective interface. However, BitBadges is not built like this. BitBadges uses a single registry and the same code is reused for all collections.

We do this for multiple reasons:

1. Security: The same code is being reused and over time, it will become more battle-tested and more secure. This is as opposed to unique smart contracts that can often have vulnerabilities, as seen with the large amount of hacks occurring in the Ethereum ecosystem.
2. Scalability: Since duplicate code doesn't need to be deployed, this solution is much more scalable.
3. Consistency: This provides a much cleaner and more consistent interface for querying, indexing, and maintenance.

Yes, this may sacrifice a little customizability, but we allow you to extend the interface and implement any custom logic required with smart contracts, if necessary. We believe the pros vastly (security, scalability, and ease of use) vastly outweigh the cons.

### **Are badges ERC-721 compatible?**

While our token standard takes inspiration from existing standards like ERC-721, our token standard has its own properties and architecture.

Our default metadata standard (what is used on the BitBadges website) does extend the ERC-721 metadata standard, so metadata should be compatible.

### **Are there plans to make an ERC for our standard?**

Although, we are not opposed to publishing our interface as a standard for other blockchain ecosystems (e.g. EIP/ERC for Ethereum), we do not have any plans currently to do so currently.

This is for a couple reasons. Firstly, our token standard is ever-evolving, so it doesn't make sense to freeze it to a permanent interface. Secondly, this would limit the standard to one ecosystem, where a main pillar of BitBadges is to be multi-chain and span across multiple blockchain ecosystems.

Feel free to use novel concepts from BitBadges in your authored improvement proposals though, as long as BitBadges is cited and co-authors (contact us).

Once development is BitBadges is complete and final, we would like to make the standard official and immutable, but we are a long ways from development being complete :)

### **What happens if there is convergence to a single blockchain?**

Personally, we believe it will always be a multi-chain world, and there will never be 100% convergence to a single ecosystem. Even if one blockchain ecosystem like Ethereum (EVM) becomes dominant, there will always be new blockchain experiments and ecosystems popping up, which means there will always be a need for multi-chain infrastructure like BitBadges.

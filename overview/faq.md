# ❓ FAQ

**Badge vs $BADGE?**

Whenever we say $BADGE, we are referring to the BitBadges blockchain token used for paying transaction fees and staking. This is not to be confused with badges / badge collections.

**Why is there a need for BitBadges?**

The answer is simple. We believe in the potential of digital blockchain tokens, but this potential cannot be realized with the current infrastructure and technology in place today ([see here](../)). BitBadges wants to help blockchain tokens realize their potential by building this infrastructure!

**What makes BitBadges better than competitors?**

See [here](../#improvements-over-existing-standards).

**Are smart contracts needed?**

No, they are not required!&#x20;

Although, you are given the option to, if you want to add custom functionality not supported by the token standard.

**Is BitBadges an L1 blockchain or an L2?**

BitBadges is its own Layer-1 blockchain built with Cosmos SDK.&#x20;

**Are badges ERC-721 compatible?**

While our token standard takes inspiration from existing standards like ERC-721, our token standard has its own properties and architecture.

Our metadata standard (what is used on the BitBadges website) does extend the ERC-721 metadata standard, so metadata will be compatible.

**What happens if there is convergence to a single blockchain?**

Personally, we believe it will always be a multi-chain world, and there will never be 100% EVM convergence. Even if one blockchain ecosystem like Ethereum (EVM) is dominant, there will always be new blockchain experiments and ecosystems popping up. Thus, we believe there will always be a need for cross-chain infrastructure like BitBadges.&#x20;

**Why the registry architecture over unique smart contracts for every collection?**

Typical NFTs and digital tokens (ERC721, ERC20) all require their own unique smart contracts to be implemented which follow their respective interface. However, BitBadges is not built like this. BitBadges uses a single registry and the same code is reused for all collections.

We do this for multiple reasons:

1. Security: The same code is being reused and over time, it will become more battle-tested and more secure. This is as opposed to unique smart contracts that can often have vulnerabilities, as seen with the large amount of hacks occurring in the Ethereum ecosystem.&#x20;
2. Scalability: Since duplicate code doesn't need to be deployed, this solution is much more scalable.&#x20;
3. Consistency: This provides a much cleaner and more consistent interface for querying, indexing, and maintenance.

Yes, this may sacrifice a little customizability, but we allow you to implement any custom logic required with smart contracts, if necessary.


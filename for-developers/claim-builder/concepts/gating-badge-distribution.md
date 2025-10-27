# Gating On-Chain Approvals

In the BitBadges site, we allow using claims to gate approvals, such as mints. For example, gate mints to those who have joined a Discord (checked via a claim).

It is important to note the hybrid apporach here. Claims are checked off-chain. BitBadges becomes the centralized oracle distributing unique claim codes to be used on-chain eventually. Thus, think of claims gating approvals as gating the "right to initiate the transfer" rather than automatically initiating it. It is a two-step process.

On-chain, these are the leaves of [Merkle challenge](../../../x-badges/concepts/approval-criteria/merkle-challenges.md) in the approval criteria and are one-time use only to prevent replay attacks. These are not the same codes as the Codes plugin, if en

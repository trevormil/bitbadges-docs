# Gating Badge Distribution

One utility offered with claims is to gate badge distribution. There are slight difeferences dependent on the balance type. &#x20;

For on-chain badge balances, a successful claim will result in reserving the right to claim, not actually completing the claim. This is because the claim criteria is checked off-chain, and we need an on-chain transfer transaction. It is a two-step process.

Behind the scenes, we reserve the unique claim code for the user. The on-chain transaction will eventually specify that claim code in the eventual transfer transaction. On-chain, these are the leaves of [Merkle challenge](../core-concepts/balances-transfers/approval-criteria/merkle-challenges.md) in the approval criteria and are one-time use only to prevent replay attacks. These are not the same codes as the Codes plugin, if enabled.

For off-chain balances, we can just allocate the balances directly after claiming.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

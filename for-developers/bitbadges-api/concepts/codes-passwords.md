# Codes / Passwords

Password / code based claims are simply [Merkle Challenges](../../collection-interface/approval-criteria.md) where the leaves of the Merkle tree are all unique codes generated by the claim creator (and thus only known to the claim creator and not public). The claim creator then distributes the codes to the users they want to receive the badges / be approved. The claimees will then provide the valid Merkle path with the unique code as leaf to be able to claim badges.



**Who stores the codes?**

&#x20;If created through the BitBadges website / indexer (which behind the scenes is done via the **POST /api/v0/addMerkleChallengeToIpfs** route), then codes will be stored by the BitBadges private, centralized servers and handed out accordingly.&#x20;

This allows us to distribute them as necessary and eliminates any storage requirement for the creator. Codes can be fetched by the manager at (**POST /api/v0/collection/:collectionId/codes**).

Note that this is provided for ease of use but also introduces another centralized trust factor. You always have the option to self-store and distribute your own codes and passwords / implement your own Merkle Challenges.

**How are password claims implemented?**

Distribution of the codes can be done via many methods, including via a centralized password solution. Passwords make it so that only one password needs to be distributed to all users for simplicity.

However, for password-based claims, to avoid replay attacks (since the blockchain is public), we still must create N unique codes via a MerkleChallenge but distribute them (one per address) to whoever enters the correct password. This is because if we use the password directly on-chain, after its first use, it is public to anyone.&#x20;

This password distribution process involves a centralized third party, which is the BitBadges indexer / API in this case. We will store the codes and distribute them to whichever users enter the correct password (**POST /api/v0/collection/:collectionId/password/:cid/:password).**

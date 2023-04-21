# 🎁 Claims

Claims define what badges in a collection can be claimed and how they can be claimed.

```protobuf
message Claim {
    repeated Balance balances = 1; 
    string codeRoot = 2;
    uint64 expectedMerkleProofLength = 10;
    string whitelistRoot = 3;
    uint64 incrementIdsBy = 4;
    uint64 amount = 5;
    repeated IdRange badgeIds = 6;
    uint64 restrictOptions = 7; 
    string uri = 8;
    IdRange timeRange = 9;
}
```

**balances** is a [**Balance\[\]**](balances.md) that represents the remaining balances for the claim. Will be decremented every time a user claims.

**badgeIds** stores which badge IDs are to be claimed for the current claim.

**amount** stores the expected balance amount received for each claim.

**incrementIdsBy** tells how much to increment the badge IDs by after every claim.

For example, if you want to distribute x1 of ID 1 to one person and then x1 of ID 2 to the next, and so on, you would set incrementIdsBy to 1.

If you want to distribute the same badge everytime, set this to 0.

**codeRoot** is a Merkle root of a tree where unique codes are leaves that are to be used to claim. To claim, one must provide a valid Merkle path to the codeRoot. Each leaf can only be used once to avoid replay attacks. If codeRoot == "", there are no codes that need to be provided. Uses SHA256.

**expectedMerkleProofLength** is the expected length of the Merkle proof provided by each user. This is to prevent second preimage attacks for Merkle trees.

**whitelistRoot** is a Merkle root of a tree where the leaves are users' addresses who are able to claim. Leaves must be the users' cosmos addresses. If whitelistRoot == "", there is no whitelist.

**restrictOptions** defines how to restrict how many times a specific user can claim.&#x20;

If 0, no restrictions. (Ex: Typically used if users can redeem more than one code)

If 1, each leaf index (of the whitelistRoot Merkle tree) can only be used once. (Ex: If you want a specific user to be able to claim twice, you can create two leaves with their address).

If 2, each address is limited to only claim once.&#x20;

**uri** is a URI that can optionally provide details about the claim. For example, on the BitBadges website, we store the Merkle Tree but only up to N-1 layers.

**timeRange** is the time when the claim is open from. Defined in seconds since the UNIX epoch.



**Examples**

**Creating whitelist / merkle trees**&#x20;

**TODO**

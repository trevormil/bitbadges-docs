# Compatibility

To make your collection compatible with the BitBadges Indexer / API (and thus the website), please use the following interfaces for hosting details off-chain.

#### Badge and Collection Metadata Format

You should host badge and collection metadata in the following JSON format at the specified URIs:

```typescript
export interface Metadata<T extends NumberType> {
  name: string;                 // Name of the badge or collection
  description: string;          // Description of the badge or collection
  image: string;                // URL to the badge or collection image
  time?: UintRange<T>;          // Time range (optional)
  validFrom?: UintRange<T>;     // Valid from range (optional)
  category?: string;            // Category (optional)
  externalUrl?: string;         // External URL (optional)
  tags?: string[];              // Tags (optional)
}
```

[https://bafybeigbmbqto74uhk2udel7azylwrlkp6x2abufgpfrvisyja4kr35niq.ipfs.dweb.link/](https://bafybeigbmbqto74uhk2udel7azylwrlkp6x2abufgpfrvisyja4kr35niq.ipfs.dweb.link/\))



If the badge metadata URI includes "{id}" anywhere in the URI, it should be dynamically replaced by the corresponding badge ID number. For example: `"...x.com/metadata/{id}"`

#### Off-Chain Balances Metadata

If your collection uses the off-chain balances type, the URI of the `offChainBalancesMetadata` should point to a JSON file that is a map of valid cosmosAddresses to Balance objects.

[https://bafybeid7cu3dw6trqapreli2myjj4g7uz7d7nwwiyx66yr2hanrxxtu5te.ipfs.dweb.link/](https://bafybeid7cu3dw6trqapreli2myjj4g7uz7d7nwwiyx66yr2hanrxxtu5te.ipfs.dweb.link/)

#### Claim Metadata

For providing additional details about a Merkle challenge claim, you can host a JSON via the `merkleChallenge.uri` field. This allows the BitBadges site to obtain certain metadata about the challenge and how to create the Merkle path to the root.

See the example [here](https://bafybeid7cu3dw6trqapreli2myjj4g7uz7d7nwwiyx66yr2hanrxxtu5te.ipfs.dweb.link/).

Note that password-based claims must be created via the BitBadges site or indexer because otherwise, the password is not known. Therefore, `hasPassword` and `password` should be falsy.

Use the `isHashed` field accordingly. To construct the Merkle path, a Merkle tree is created with leaves. If `isHashed = true`, the user's inputted secret code (distributed to them) is hashed, matched to a leaf, and used to construct the Merkle path. Do not reveal any secret codes via leaves (Merkle leaves) here, as whatever is stored here is publicly visible.

#### Merkle Challenge Details

```typescript
export interface MerkleChallengeDetails<T extends NumberType> {
  name: string;                   // Name of the Merkle challenge
  description: string;            // Description of the Merkle challenge
  challengeDetails: ChallengeDetails<T>;
}

export interface ChallengeDetails<T extends NumberType> {
  leavesDetails: LeavesDetails;
}

export interface LeavesDetails {
  leaves: string[];               // Array of leaf data
  isHashed: boolean;              // Indicates whether the leaf data is hashed
}
```

IMPORTANT: Also make sure your Merkle tree follows the expected format (see [**Approval Criteria - Merkle Challenges**](../for-developers/concepts/approval-criteria.md)**).**\

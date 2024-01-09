# Designing for Compatibility

To make your collection compatible with the BitBadges Indexer / API (and thus the official website), please make sure everything is compatible when hosting details off-chain.

#### Badge and Collection Metadata Format

{% content-ref url="../core-concepts/metadata.md" %}
[metadata.md](../core-concepts/metadata.md)
{% endcontent-ref %}

#### Off-Chain Balances Metadata

{% content-ref url="../core-concepts/balance-types.md" %}
[balance-types.md](../core-concepts/balance-types.md)
{% endcontent-ref %}

{% content-ref url="../tutorials/create-and-host-off-chain-balances.md" %}
[create-and-host-off-chain-balances.md](../tutorials/create-and-host-off-chain-balances.md)
{% endcontent-ref %}

#### Approval / Claim Metadata

For providing additional details about an approval, you can host a JSON via the **`approval.uri`** field. This allows the BitBadges site to obtain certain metadata about the approval and how to create the Merkle path to the root (if applicable). See the example [here](https://bafybeid7cu3dw6trqapreli2myjj4g7uz7d7nwwiyx66yr2hanrxxtu5te.ipfs.dweb.link/).

Note that password-based claims must be created via the BitBadges site or indexer because otherwise, the password is not known. Therefore, `hasPassword` and `password` should be falsy.

Use the `isHashed` field accordingly. To construct the Merkle path, a Merkle tree is created with leaves. If `isHashed = true`, the user's inputted secret code (distributed to them) is hashed, matched to a leaf, and used to construct the Merkle path. Do not reveal any secret codes via leaves (Merkle leaves) here, as whatever is stored here is publicly visible.

#### Merkle Challenge Details

<pre class="language-typescript"><code class="lang-typescript">import { Options as MerkleTreeJsOptions } from "merkletreejs/dist/MerkleTree";

export interface MerkleChallengeDetails&#x3C;T extends NumberType> {
  name: string;                   // Name of the Merkle challenge
  description: string;            // Description of the Merkle challenge
  challengeDetails: ChallengeDetails&#x3C;T>;
}

export interface ChallengeDetails&#x3C;T extends NumberType> {
  leavesDetails: LeavesDetails;
<strong>  treeOptions?: MerkleTreeJsOptions 
</strong>}

export interface LeavesDetails {
  leaves: string[];               // Array of leaf data
  isHashed: boolean;              // Indicates whether the leaf data is hashed
}
</code></pre>

Note that the indexer uses 'merkletreejs' NPM package to construct and build the Merkle trees accordng to the code below. You can use **treeOptions** to make sure the tree is built correctly.

<pre class="language-typescript"><code class="lang-typescript">import SHA256 from 'crypto-js/sha256';
import MerkleTree from 'merkletreejs';

<strong>new MerkleTree(leavesDetails?.leaves.map(x => {
</strong>      return leavesDetails?.isHashed ? x : SHA256(x);
  }) ?? [],
  SHA256,
  treeOptions
)
</code></pre>

# Designing for Compatibility

To make your collection compatible with the BitBadges Indexer / API (and thus the official website), please make sure everything is compatible when hosting details off-chain.

#### Badge and Collection Metadata Format

{% content-ref url="../../core-concepts/metadata.md" %}
[metadata.md](../../core-concepts/metadata.md)
{% endcontent-ref %}

#### Off-Chain Balances Metadata

{% content-ref url="../../core-concepts/balance-types.md" %}
[balance-types.md](../../core-concepts/balance-types.md)
{% endcontent-ref %}

{% content-ref url="../../tutorials/create-and-host-off-chain-balances.md" %}
[create-and-host-off-chain-balances.md](../../tutorials/create-and-host-off-chain-balances.md)
{% endcontent-ref %}

#### Approval Metadata

For providing additional details about an approval, you can host a JSON via the **`approval.uri`** field. This allows the BitBadges site to obtain certain metadata about the approval. Markdown is supported for description.

<pre><code><strong>{
</strong>    "name": "....",
    "description": "...."
}
</code></pre>

#### Merkle Challenge Details

In a JSON hosted at **merkleChallenge.uri,** you can also provide Merkle challenge details if you have self-created a Merkle challenge, such as self-generating codes / passwords. This is only needed if you want compatibility with the BitBadges site and to be able to claim / transfer w/ this Merkle challenge directly on the site. We need to know how to generate the Merkle path for users.

<pre class="language-typescript"><code class="lang-typescript"><strong>{
</strong>
    "treeOptions": MerkleTreeJsOptions, // see below
    "isHashed": boolean;
    "leaves": string[];

}
</code></pre>

IMPORTANT: Do not reveal any secret codes via **leaves** (Merkle leaves) here, as whatever is stored here is publicly visible.

Whitelist trees: **leaves** are the Cosmos addresses and **isHashed** is false.

Codes trees: **leaves** are the secret codes SHA256 hashed once and **isHashed** is true. KEEP THE PREIMAGES (SECRET CODES) PRIVATE. The only thing that should be publicly visible are the hashed codes.

**Tree Options**

We use the 'merkletreejs' NPM package to construct and build the Merkle trees accordng to the code below. You can use **treeOptions** to make sure the tree is built correctly.

<pre class="language-typescript"><code class="lang-typescript"><strong>import { Options as MerkleTreeJsOptions } from "merkletreejs/dist/MerkleTree";
</strong></code></pre>

<pre class="language-typescript"><code class="lang-typescript">import SHA256 from 'crypto-js/sha256';
import MerkleTree from 'merkletreejs';

<strong>new MerkleTree(leavesDetails?.leaves.map(x => {
</strong>      return leavesDetails?.isHashed ? x : SHA256(x);
  }) ?? [],
  SHA256,
  treeOptions
)
</code></pre>

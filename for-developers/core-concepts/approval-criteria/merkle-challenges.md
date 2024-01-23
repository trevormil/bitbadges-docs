# Merkle Challenges

```typescript
export interface MerkleChallenge<T extends NumberType> {
  root: string
  expectedProofLength: T;
  useCreatorAddressAsLeaf: boolean
  maxUsesPerLeaf: T
  uri: string
  customData: string
}
```

<pre class="language-json"><code class="lang-json"><strong>"merkleChallenge": {
</strong>   "root": "758691e922381c4327646a86e44dddf8a2e060f9f5559022638cc7fa94c55b77",
   "expectedProofLength": "1",
   "useCreatorAddressAsLeaf": true,
   "maxOneUsePerLeaf": true,
   "uri": "ipfs://Qmbbe75FaJyTHn7W5q8EaePEZ9M3J5Rj3KGNfApSfJtYyD",
   "customData": ""
}
</code></pre>

Merkle challenges allow you to define a SHA256 Merkle tree, and to be approved for each transfer, the initiator of the transfer must provide a valid Merkle path for the tree when they transfer (via **merkleProofs** in [MsgTransferBadges](../../create-and-broadcast-txs/cosmos-sdk-msgs/msgtransferbadges.md)).

For example, you can create a Merkle tree of claim codes. Then to be able to claim badges, each claimee must provide a valid Merkle path from the claim code to the **root**. You distribute the secret leaves in any method you prefer. Or, you can create an whitelist tree where the user's addresses are the leaves, and they must specify the valid Merkle path from their address to claim.

#### Expected Proof Length

The **expectedProofLength** defines the expected length for the Merkle proofs to be provided. This is to avoid preimage and second preimage attacks. **All proofs must be of the correct length, which means you must design your trees accordingly. THIS IS CRITICAL.**&#x20;

**Whitelist Trees**

First, you can set **useCreatorAddressAsLeaf**. If set, we will override the provided leaf of each Merkle proof with the msg.creator aka the Cosmos address of the initiator of the transaction. This can be used to implement whitelist trees. Note that the initiator must also be within the **initiatedByList** of the approval for it to make sense.

For whitelist trees (**useCreatorAddressAsLeaf** is true), **maxUsesPerLeaf** can be set to any number. "0" or null means unlimited uses. "1" means max one use per leaf and so on. When **useCreatorAddressAsLeaf** is false, this must be set to "1" to avoid replay attacks.For example, ensure that a code / proof can only be used once because once used once, the blockchain is public and anyone then knows the secret code.

We track this in a challenge tracker, similar to the approvals trackers previously explained. We simply track if a leaf index (leftmost leaf of expected proof length layer (aka leaf layer) = index 0, ...) has been used and only allow it to be used **maxUsesPerLeaf** many times, if constrained. Like approval trackers, this is increment only and non-deletable.

The identifier for each challenge tracker consists of **challengeTrackerId** along with other identifying details. For simplicity, we reccomend keeping this the same as the approval's **approvalId,** unless implementing advanced functionality. The **challengeTrackerId** cannot be the same as a different approval's **approvalId.**

```typescript
{
  collectionId: T;
  approvalLevel: "collection" | "incoming" | "outgoing";
  approverAddress?: string;
  challengeTrackerId: string;
  leafIndex: T;
}
```

**approvalLevel** corresponds to whether it is a collection-level approval, user incoming approval, or user outgoing approval. If it is user level, the **approverAddress** is the user setting the approval. **approverAddress** is blank for collection level.

Example:

`1-collection- -uniqueID-0` -> USED 1 TIME

`1-collection- -uniqueID-1` -> UNUSED

**Reserving Specific Leafs**

See Predetermined Balances below for reserving specific leaf indices for specific badges.

#### **Creating a Merkle Tree**

We provide the **treeOptions** field in the SDK to let you define your own build options for the tree (see [Compatibility](../../bitbadges-api/designing-for-compatibility.md) with the BitBadges API / Indexer). You may experiment with this, but please test all Merkle paths and claims work as intended first. The only tested build options so far are what you see below with the fillDefaultHash.

The important part is making sure all leaves are on the same layer and have the same proof length, or else, they will fail on-chain.

```typescript
import { SHA256 } from 'crypto-js';
import MerkleTree from 'merkletreejs';

const codes = [...]
const hashedCodes = codes.map(x => SHA256(x).toString());
const treeOptions = { fillDefaultHash: '0000000000000000000000000000000000000000000000000000000000000000' }
const codesTree = new MerkleTree(hashedCodes, SHA256, treeOptions);
const codesRoot = codesTree.getRoot().toString('hex');
const expectedMerkleProofLength = codesTree.getLayerCount() - 1;
```

For whitelists, replace with this code.

```typescript
addresses.push(...toAddresses.map(x => convertToCosmosAddress(x)));

const addressesTree = new MerkleTree(addresses.map(x => SHA256(x)), SHA256, treeOptions)
const addressesRoot = addressesTree.getRoot().toString('hex');
```

A valid proof can then be created via where codeToSubmit is the code submitted by the user.

```typescript
const passwordCodeToSubmit = '....'
const leaf = isWhitelist ? SHA256(chain.cosmosAddress).toString() : SHA256(passwordCodeToSubmit).toString();
const proofObj = tree?.getProof(leaf, whitelistIndex !== undefined && whitelistIndex >= 0 ? whitelistIndex : undefined);
const isValidProof = proofObj && tree && proofObj.length === tree.getLayerCount() - 1;


const codeProof = {
  aunts: proofObj ? proofObj.map((proof) => {
    return {
      aunt: proof.data.toString('hex'),
      onRight: proof.position === 'right'
    }
  }) : [],
  leaf: isWhitelist ? '' : passwordCodeToSubmit,
}

const txCosmosMsg: MsgTransferBadges<bigint> = {
  creator: chain.cosmosAddress,
  collectionId: collectionId,
  transfers: [{
    ...
    merkleProofs: requiresProof ? [codeProof] : [],
    ...
  }],
};
```

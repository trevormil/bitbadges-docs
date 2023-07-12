# Merkle Challenges

### **Pre-Readings**

See [Claims](../must-know-concepts/claims.md).

### **Creating a Merkle Tree for Claims**

With the following snippet, this creates a MerkleTree in JavaScript that is compatible with the Merkle challenges for approvals. This can be similarly applied to whitelists, just replace the leaves with the Cosmos addresses (and make sure to turn the useCreatorAddressAsLeaf option on in the approval).

To prevent the same Merkle path from being used multiple times (if desired), you can set the maxOneUsePerLeaf option.

IMPORTANT: The value in fillDefaultHash must be a hash with no known preimage, such as "0000000000000000000000000000000000000000000000000000000000000000" as used below. If there are N leaves with fillDefaultHash and the preimage is known, the preimage can be used as a valid code to claim badges N times by providing a Merkle path from each leaf.

```typescript
import { SHA256 } from 'crypto-js';
import MerkleTree from 'merkletreejs';
import { MessageMsgClaimBadge } from 'bitbadgesjs-transactions';

const codes = [...]
const hashedCodes = codes.map(x => SHA256(x).toString());
const codesTree = new MerkleTree(hashedCodes, SHA256, { fillDefaultHash: '0000000000000000000000000000000000000000000000000000000000000000' });
const codesRoot = codesTree.getRoot().toString('hex');
const expectedMerkleProofLength = codesTree.getLayerCount() - 1;
```

A valid proof can then be created via where **codeToSubmit** is the code submitted by the user.

```typescript
const leafString = SHA256(codeToSubmit).toString();
const codeProofObj = codeTree?.getProof(leafString);
const isValidCodeProof = codeProofObj && codeTree && codeProofObj.length === codeTree.getLayerCount() - 1;

const codeProof = {
    aunts: isValidCodeProof ? codeProofObj?.map((proof) => {
        return {
            aunt: proof.data.toString('hex'),
            onRight: proof.position === 'right'
        }
    }) : [],
    leaf: isValidCodeProof ? codeToSubmit : '',
}
```



**Using Leaf Index for Predetermined Balances**

You can choose to use the leaf index for distribution order as an option via predeterminedBalances.OrderCalculationMethod. This can be used to reserve specific badges / amounts for specific leaves.&#x20;

The leaf index 0 corresponds to the leftmost leaf of the layer which matches the expectedProofLength. And increments by 1 every leaf as we move to the right.

For example, if we have a simple tree with (root) -> (leaf1, leaf2) and expectedProofLength = 1. leaf1 would have index 0. leaf2 would have index 1.


# Merkle Trees for Claims

### **Creating a Merkle Tree for Claims**

With the following snippet, this creates a MerkleTree of **codes**. This can be similarly applied to whitelists, just replace codes with the Cosmos addresses.

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

const txCosmosMsg: MessageMsgClaimBadge = {
    ...
    codeProof: {
        aunts: isValidCodeProof ? codeProofObj?.map((proof) => {
            return {
                aunt: proof.data.toString('hex'),
                onRight: proof.position === 'right'
            }
        }) : [],
        leaf: isValidCodeProof ? codeToSubmit : '',
    }
};
```

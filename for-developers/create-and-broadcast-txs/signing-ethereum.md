# Signing - Ethereum

Pre-Requisite: You have generated the transaction and have it in the **txn** variable (see prior pages).

#### Signing with Metamask

After creating the transaction we need to send the payload to metamask so it can be signed. With that signature we are going to add a Web3Extension to the Cosmos Transactions and broadcast it to the Cosmos node. These extensions are used to tell the blockchain to parse it as an Ethereum signature.

```ts
// Follow the previous step to generate the txn object
import { cosmosToEth } from 'bitbadgesjs-utils'
import {
  generateEndpointBroadcast,
  generatePostBodyBroadcast,
} from 'bitbadgesjs-utils'
import {
  createTxRawEIP712,
  signatureToWeb3Extension,
} from 'bitbadgesjs-proto'
```

**Option 1: Metamask / Ethers**

<pre class="language-typescript"><code class="lang-typescript">//Add necessary imports
import { _TypedDataEncoder } from 'ethers/lib/utils.js';

// Init Metamask
await window.ethereum.enable()

//Option 1: window.ethereum
const eip = _TypedDataEncoder.getPayload(txn.eipToSign.domain, types_, txn.eipToSign.message);
const sig = await window.ethereum.request({
    method: 'eth_signTypedData_v4',
    params: [cosmosToEth(sender.accountAddress), JSON.stringify(eip)],
})

//Option 2: Use the signer._signTypedData from ethers directly (https://docs.ethers.org/v5/api/signer/#Signer-signTypedData)
//It will give an error with an unused EIP712Domain type, so you have to remove that before calling it as seen below.
//It adds this type automatically.
<strong>
</strong>//From https://github.com/wagmi-dev/wagmi/blob/main/packages/core/src/actions/accounts/signTypedData.ts#L41
const types_ = Object.entries(txn.eipToSign.types)
    .filter(([key]) => key !== 'EIP712Domain')
    .reduce((types, [key, attributes]: [string, TypedDataField[]]) => {
      types[key] = attributes.filter((attr) => attr.type !== 'EIP712Domain')
      return types
    }, {} as Record&#x3C;string, TypedDataField[]>)

// Method name may be changed in the future, see https://docs.ethers.io/v5/api/signer/#Signer-signTypedData
return await signer._signTypedData(txn.eipToSign.domain, types_, txn.eipToSign.message)
</code></pre>

**Option 2: WalletConnect / WAGMI**

Pre-Req: For this tutorial, your dApp must be using WalletConnect. See [their docs](https://docs.walletconnect.com/2.0) for setup.&#x20;

```typescript
import { signTypedData } from "@wagmi/core";

const sig = await signTypedData({
    value: txn.eipToSign.message,
    types: txn.eipToSign.types,
    domain: txn.eipToSign.domain
});
```

**Generating and Broadcasting**

```typescript
// The chain and sender objects are the same as the previous example
let extension = signatureToWeb3Extension(chain, sender, signature)

// Create the txRaw
let txRaw = createTxRawEIP712(
  txn.legacyAmino.body,
  txn.legacyAmino.authInfo,
  extension,
)
```

### Output

This will leave you with a **txRaw** variable which is to be submitted to a running blockchain node. See [Broadcast to a Node.](broadcast-to-a-node.md)

### Full Snippet

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/contexts/chains/EthereumContext.tsx" %}

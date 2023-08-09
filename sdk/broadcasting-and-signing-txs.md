# Broadcasting and Signing Txs

To learn more about broadcasting transactions with Cosmos SDK, you can visit [https://docs.cosmos.network/v0.46/run-node/txs.html](https://docs.cosmos.network/v0.46/run-node/txs.html).

For the transaction Msg types offered by BitBadges, see [Tx Msg Interfaces](../for-developers/concepts/msgs.md).

The recommended way to broadcast a transaction is by simply visiting [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast). This is a simple interface to just copy and paste your pregenerated Msgs into. The gas, transaction signers, signing the transaction is all handled via the interface. All you need to provide is copy and paste your Msg.&#x20;

You can also use the [BitBadges SDK](broken-reference) for more programmatic broadcasting**.** The SDK provides easy-to-use TypeScript functions to construct transactions of all types and broadcast them to a blockchain node.

Note the examples below show how to broadcast directly to any running blockchain node. For broadcasting to the BitBadges blockchain node, in the examples below, simply replace&#x20;

<pre class="language-typescript"><code class="lang-typescript"><strong>`http://URL:1317${generateEndpointBroadcast()}`
</strong></code></pre>

with&#x20;

```typescript
https://api.bitbadges.io/api/v0/broadcast
```

### Examples

**Fetching Account Details**

To fetch a user's account details, the easiest way is to use the routes from the BitBadges API in [Users](../indexer-api/api/users.md). You can also query a node directly.

This will return the user's cosmos address, account ID, sequence (nonce), and public key.&#x20;

For a user who has not submitted a transaction yet, the fetched public key will be null. You can get the public key as shown below.&#x20;

Note that the user will need to be registered to submit a transaction (must first receive $BADGE to pay for gas). This will assign them an account number.



**Get Public Key - Metamask / ETH**

```typescript
const getPublicKey = async (cosmosAddress: string) => {
    const message = 'Please sign this message, so we can generate your public key';

    let sig = await window.ethereum.request({
        method: 'personal_sign',
        params: [message, cosmosToEth(cosmosAddress)],
    })

    const msgHash = ethers.utils.hashMessage(message);
    const msgHashBytes = ethers.utils.arrayify(msgHash);
    const pubKey = ethers.utils.recoverPublicKey(msgHashBytes, sig);


    const pubKeyHex = pubKey.substring(2);
    const compressedPublicKey = Secp256k1.compressPubkey(new Uint8Array(Buffer.from(pubKeyHex, 'hex')));
    const base64PubKey = Buffer.from(compressedPublicKey).toString('base64')
    return base64PubKey;
}
```

**Get Public Key - Keplr / Cosmos**

```typescript
import { CHAIN_DETAILS } from 'bitbadgesjs-utils';

const getPublicKey = async (_cosmosAddress: string) => {
    const chain = CHAIN_DETAILS;
    
    const account = await window?.keplr?.getKey(chain.cosmosChainId)
    if (!account) return '';
    const pk = Buffer.from(account.pubKey).toString('base64')
    
    return pk;
}
```

#### Create a Transaction

The transaction can be signed using EIP712 on Metamask and SignDirect on Keplr.

For any other transaction types on BitBadges, just replaces **createMessageSend** with your desired create Tx function (**createTxMsgDeleteCollection, ...)** and update the parameters accordingly.

```ts
import { createMessageSend } from 'bitbadgesjs-proto'

const chain = {
  chainId: 2,
  cosmosChainId: 'bitbadges_1-2',
}

const sender = {
  accountAddress: 'cosmos....',
  sequence: 1,
  accountNumber: 9,
  pubkey: '....', 
}

const fee = {
  amount: '20',
  denom: 'badge',
  gas: '200000',
}

const memo = ''

const params = {
  destinationAddress: 'cosmos1pmk2r32ssqwps42y3c9d4clqlca403yd9wymgr',
  amount: '1',
  denom: 'badge',
}

const msg = createMessageSend(chain, sender, fee, memo, params)

// msg.signDirect is the transaction in Keplr format
// msg.legacyAmino is the transaction with legacy amino
// msg.eipToSign is the EIP712 data to sign with metamask
```

#### Signing with Metamask

After creating the transaction we need to send the payload to metamask so it can be signed. With that signature we are going to add a Web3Extension to the Cosmos Transactions and broadcast it to the Cosmos node.



```ts
// Follow the previous step to generate the msg object
import { cosmosToEth } from 'bitbadgesjs-address-converter'
import {
  generateEndpointBroadcast,
  generatePostBodyBroadcast,
} from 'bitbadgesjs-provider'
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

Pre-Req: Your dApp must be using WalletConnect. See [their docs](https://docs.walletconnect.com/2.0) for setup.

```typescript
import { signTypedData } from "@wagmi/core";

const sig = await signTypedData({
    value: txn.eipToSign.message,
    types: txn.eipToSign.types,
    domain: txn.eipToSign.domain
});
```

**Generating and Broadcasting**

<pre class="language-typescript"><code class="lang-typescript">// The chain and sender objects are the same as the previous example
let extension = signatureToWeb3Extension(chain, sender, signature)

// Create the txRaw
let rawTx = createTxRawEIP712(
  msg.legacyAmino.body,
  msg.legacyAmino.authInfo,
  extension,
)

// Broadcast it
const postOptions = {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: generatePostBodyBroadcast(rawTx),
}

let broadcastPost = await fetch(
<strong>  //replace with your node URL or https://api.bitbadges.io/v0/broadcast
</strong><strong>  `http://localhost:1317${generateEndpointBroadcast()}`, 
</strong>  postOptions,
)
let response = await broadcastPost.json()
</code></pre>

#### Signing with Keplr

```ts
// Follow the previous step to generate the msg object
import { createTxRaw } from 'bitbadgesjs-proto'
import {
  generateEndpointBroadcast,
  generatePostBodyBroadcast,
} from 'bitbadgesjs-provider'

let sign = await window?.keplr?.signDirect(
  chain.cosmosChainId,
  sender.accountAddress,
  {
    bodyBytes: msg.signDirect.body.serializeBinary(),
    authInfoBytes: msg.signDirect.authInfo.serializeBinary(),
    chainId: chain.cosmosChainId,
    accountNumber: new Long(sender.accountNumber),
  },
  // @ts-expect-error the types are not updated on Keplr side
  { isEthereum: true },
)

if (sign !== undefined) {
  let rawTx = createTxRaw(sign.signed.bodyBytes, sign.signed.authInfoBytes, [
    new Uint8Array(Buffer.from(sign.signature.signature, 'base64')),
  ])

  // Broadcast it
  const postOptions = {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: generatePostBodyBroadcast(rawTx),
  }

  let broadcastPost = await fetch(
    //replace with your node URL or https://api.bitbadges.io/v0/broadcast
    `http://localhost:1317${generateEndpointBroadcast()}`,
    postOptions,
  )
  let response = await broadcastPost.json()
}
```


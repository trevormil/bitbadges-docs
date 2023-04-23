# Broadcasting and Signing Txs

To learn more about broadcasting transactions with Cosmos SDK, you can visit [https://docs.cosmos.network/v0.46/run-node/txs.html](https://docs.cosmos.network/v0.46/run-node/txs.html).

For the transaction Msg types offered by BitBadges, see [Tx Msg Interfaces](../for-developers/need-to-know/msgs.md).

The recommended way to broadcast a transaction is by using the [BitBadges SDK](broken-reference)**.** The SDK provides easy-to-use TypeScript functions to construct transactions of all types and broadcast them to a blockchain node.

Note the examples below show how to broadcast directly to a running blockchain node. For broadcasting via the BitBadges blockchain node, in the examples below, simply replace

<pre class="language-typescript"><code class="lang-typescript"><strong>`http://URL:1317${generateEndpointBroadcast()}`
</strong></code></pre>

with&#x20;

```typescript
https://api.bitbadges.io/api/v0/broadcast
```

### Examples

#### Create a Transaction

The transaction can be signed using EIP712 on Metamask and SignDirect on Keplr.

For any other transaction types on BitBadges, just replaces **createMessageSend** with your desired create Tx function (**createTxMsgDeleteCollection, createTxMsgUpdateUris, ...)** and update the parameters accordingly.

```ts
import { createMessageSend } from 'bitbadgesjs-transactions'

const chain = {
  chainId: 2,
  cosmosChainId: 'bitbadges_1-2',
}

const sender = {
  accountAddress: 'cosmos....',
  sequence: 1,
  accountNumber: 9,
  pubkey: 'AgTw+4v0daIrxsNSW4FcQ+IoingPseFwHO1DnssyoOqZ',
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
} from 'bitbadgesjs-transactions'

// Init Metamask
await window.ethereum.enable()

// Request the signature
let signature = await window.ethereum.request({
  method: 'eth_signTypedData_v4',
  params: [cosmosToEth(sender.accountAddress), JSON.stringify(msg.eipToSign)],
})

// The chain and sender objects are the same as the previous example
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
  `http://localhost:1317${generateEndpointBroadcast()}`,
  postOptions,
)
let response = await broadcastPost.json()
```

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
    `http://localhost:1317${generateEndpointBroadcast()}`,
    postOptions,
  )
  let response = await broadcastPost.json()
}
```


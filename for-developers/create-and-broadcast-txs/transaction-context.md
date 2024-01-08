# Transaction Context

You have two options for generating, signing, and brodcasting messages.

1. Use  [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast) - This is a visual UI that you can simply copy and paste your transaction Msg contents into. Generating all additional transaction details, gas, fees, and signing is all outsourced to the user interface. This is the recommended option if you do not require programmatically submitting TXs.
2. Generate, sign, and broadcast directly to a running blockchain node. This is more technical and has more steps but can be done programmatically.&#x20;

If you plan to use Option 1, you may proceed to the next page because generating the transaction context is already handled via the user interface.

If you plan to use option 2, see below.

### Generating Transaction Context

The first step is to fetch and identify the transaction context and the account details for who is going to sign. You will need the following information below.

```typescript
import { createTxMsgSend, SupportedChain } from 'bitbadgesjs-proto'

const chain = {
  chainId: 2,
  cosmosChainId: 'bitbadges_1-2',
  chain: SupportedChain.ETH //signing chain
}

const sender = {
  accountAddress: 'cosmos....', //Must be the cosmos address
  sequence: 1,
  accountNumber: 9,  
  pubkey: '....', //base64 public key
}

const fee = {
  amount: '20',
  denom: 'badge',
  gas: '200000',
}

const memo = ''
```

```typescript
export interface TxContext {
  chain: Chain
  sender: Sender
  fee: Fee
  memo: string
}
```

```typescript
export interface Chain {
  chainId: number
  cosmosChainId: string
  chain: SupportedChain
}
```

```typescript
export enum SupportedChain {
  ETH = 'Ethereum',
  COSMOS = 'Cosmos',
  SOLANA = 'Solana',
  UNKNOWN = 'Unknown',
}
```

```typescript
export interface Sender {
  accountAddress: string
  sequence: number
  accountNumber: number
  pubkey: string
}
```

**Fee**

Generating the fee can be tricky. It should be reasonable for the current gas prices but also not too expensive.&#x20;

To get the **gas**, we recommend simulating the transaction right before broadcasting to see how much gas it uses on a dry run. We will walk you through how to do this in the broadcast tutorial.

```typescript
const fee = {
  amount: '20',
  denom: 'badge',
  gas: '200000',
}
```

**Chain Details**

<pre class="language-typescript"><code class="lang-typescript">import { SupportedChain, MAINNET_CHAIN_DETAILS, BETANET_CHAIN_DETAILS } from "bitbadgesjs-utils";
<strong>
</strong><strong>const chain = { ...MAINNET_CHAIN_DETAILS, chain: SupportedChain.SOLANA };
</strong></code></pre>

The chain details define which chain you are broadcasting to (testnet vs mainnet). You will also specify the native chain of the user signing the transaction. This lets us know how to parse the transaction and what the expected signing format is.

**Sender Details**

To fetch a user's account details, the easiest way is to use the routes from the BitBadges API in [Users](broken-reference). You can also query a node directly.

This will return the user's cosmos address, account ID, sequence (nonce), and public key. If the user has previously interacted with the blockchain, all this information will already be populated.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption><p>Note how publicKey is empty. This is because the user has not interacted with the chain yet.</p></figcaption></figure>

IMPORTANT: For a user who has not yet interacted with the blockchain, the fetched public key will be null and accountNumber will be -1.&#x20;

To get an account number, they need to receive $BADGE somehow (this is also a pre-requisite to pay for any gas fees).

To get the public key, see below. Note public keys are expected to be in base64 format.

#### **Get Public Key - Metamask / ETH**

For MetaMask and Ethereeum, you will need to get the user to sign a message and recover the public key from the signature and convert to base64.

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

#### **Get Public Key - Keplr / Cosmos**

For Keplr / Cosmos, you can simply use getKey() then convert to base64,

```typescript
import { CHAIN_DETAILS } from 'bitbadgesjs-utils';

const getPublicKey = async (_cosmosAddress: string) => {
    const chain = CHAIN_DETAILS; //can also use BETANET_CHAIN_DETAILS or MAINNET_CHAIN_DETAILS
    
    const account = await window?.keplr?.getKey(chain.cosmosChainId)
    if (!account) return '';
    const pk = Buffer.from(account.pubKey).toString('base64')
    
    return pk;
}
```

#### **Get Public Key - Solana**

<pre class="language-typescript"><code class="lang-typescript">const bs58 = require('bs58');
<strong>
</strong><strong>//Pre: get provider from Phantom wallet
</strong><strong>
</strong><strong>const resp = await provider.request({ method: "connect" });
</strong>const solanaPublicKeyBase58 = resp.publicKey.toBase58(); //base58 public key
const solanaPublicKeyBuffer = bs58.decode(solanaPublicKeyBase58);
const publicKeyToSet = Buffer.from(solanaPublicKeyBuffer).toString('base64')
</code></pre>

# 👥 Accounts (Low-Level)

## Pre-Readings

* [Cosmos SDK Accounts](https://docs.cosmos.network/main/basics/accounts)
* [Ethereum Accounts](https://ethereum.org/en/whitepaper/#ethereum-accounts)

### Accounts[​](https://docs.injective.network/learn/basic-concepts/accounts#injective-accounts) <a href="#injective-accounts" id="injective-accounts"></a>

For accounts (standard senders of transactions) , we support users from four L1 blockchain ecosystems currently (Ethereum, Bitcoin, Solana, and Cosmos). These are mapped behind the scenes through mapping addresses to an equivalent BitBadges address and being signature compatible (able to verify signatures of the native signature schemes).

### Signing Transactions <a href="#injective-accounts" id="injective-accounts"></a>

For non-Cosmos signatures, we map everything to a content hash to get a string such as:

```
This is a BitBadges transaction with the content hash: b0d2944e1e367cc394d0e305f94eccf543983265a32b5cb71800da7d6df57679
```

We then verify the signatures of the transaction string as a personal message signature via the respective signature algorithm (e.g. ECDSA for Ethereum).

### **Cosmos**

Normal Cosmos accounts are also supported with all the Cosmos SDK's native functionality. We refer you to their documentation for further information.

### **Ethereum**

BitBadges allows Ethereum addresses to use Ethereum's ECDSA secp256k1 curve for keys.

### **Solana**

BitBadges also extends the SDK's functionality to support Solana signatures signing with a ed25519 key. Addresses are expected to be in the native Base58 format.

### Bitcoin

BitBadges supports Bitcoin P2WPKH addresses and BIP322 signature verification.

### **Public Key Types**

For standard Cosmos accounts and Bitcoin accounts, the public key will have the `"@type": "/cosmos.crypto.secp256k1.PubKey"`.

For Solana accounts, the public key will have the `"@type": "/cosmos.crypto.ed25519.PubKey"`.

For standard Ethereum accounts, the public key will have the `"@type": "/ethereum.PubKey"`.

`{"@type":"/ethereum.PubKey","key":"AsV5oddeB+hkByIJo/4lZiVUgXTzNfBPKC73cZ4K1YD2"}`

### Deriving BitBadges Ethereum Account from a private key/mnemonic[​](https://docs.injective.network/learn/basic-concepts/accounts#deriving-injective-account-from-a-private-keymnemonic) <a href="#deriving-injective-account-from-a-private-keymnemonic" id="deriving-injective-account-from-a-private-keymnemonic"></a>

Below you will see an example code snippet on how to derive a BitBadges Account from a private key and/or a mnemonic phase:

```typescript
import { Wallet } from 'ethers'
import { Address as EthereumUtilsAddress } from 'ethereumjs-util'

const mnemonic = "indoor dish desk flag debris potato excuse depart ticket judge file exit"
const privateKey = "afdfd9c3d2095ef696594f6cedcae59e72dcd697e2a7521b1578140422a4f890"
const defaultDerivationPath = "m/44'/60'/0'/0/0"
const defaultBech32Prefix = 'bb'
const isPrivateKey: boolean = true /* just for the example */

const wallet = isPrivateKey ? Wallet.fromMnemonic(mnemonic, defaultDerivationPath) : new Wallet(privateKey)
const ethereumAddress = wallet.address
const addressBuffer = EthereumUtilsAddress.fromString(ethereumAddress.toString()).toBuffer()
const bitbadgesAddress = bech32.encode(defaultBech32Prefix, bech32.toWords(addressBuffer))
```

Let's see an example code snipped on how to derive a public key from a private key:

```typescript
import secp256k1 from 'secp256k1'

const privateKey = "afdfd9c3d2095ef696594f6cedcae59e72dcd697e2a7521b1578140422a4f890"
const privateKeyHex = Buffer.from(privateKey.toString(), 'hex')
const publicKeyByte = secp256k1.publicKeyCreate(privateKeyHex)

const buf1 = Buffer.from([10])
const buf2 = Buffer.from([publicKeyByte.length])
const buf3 = Buffer.from(publicKeyByte)

const publicKey = Buffer.concat([buf1, buf2, buf3]).toString('base64')
const type = '/ethereum.PubKey'
```

#### Acknowledgements

Credit to [https://docs.injective.network/learn/basic-concepts/accounts](https://docs.injective.network/learn/basic-concepts/accounts) and [https://docs.evmos.org/protocol/concepts/accounts](https://docs.evmos.org/protocol/concepts/accounts).

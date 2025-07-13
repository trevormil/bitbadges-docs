# 👥 Accounts (Technical)

## Pre-Readings

* [Cosmos SDK Accounts](https://docs.cosmos.network/main/basics/accounts)
* [Ethereum Accounts](https://ethereum.org/en/whitepaper/#ethereum-accounts)

### Accounts and Validator Operators

For accounts (standard senders of transactions) and validator operators, we support users from four L1 blockchain ecosystems currently (Ethereum, Bitcoin, Solana, and Cosmos).

### **Ethereum**

BitBadges allows Ethereum addresses to use Ethereum's ECDSA secp256k1 curve for keys. The public key for these accounts will be a custom type (forked from [Ethermint](https://github.com/cosmos/ethermint)). This satisfies the [EIP84](https://github.com/ethereum/EIPs/issues/84) for full [BIP44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) paths. The root HD path for BitBadges Ethereum-based accounts is `m/44'/60'/0'/0`. BitBadges uses the Coin type `60` to support Ethereum type accounts, unlike other Cosmos chains that use Coin type `118.`

**Signing Method:** All transactions should be signed with EIP712. EIP712 transactions can be generated via the BitBadges SDK.

### **Solana**

BitBadges also extends the SDK's functionality to support Solana signatures signing with a ed25519 key. Addresses are expected to be in the native Base58 format.

**Signing Method:** Transactions will be signed in JSON stringified format with all keys alphabetically sorted. JSON messages can also be generated via the SDK.

### **Cosmos**

Normal Cosmos accounts are also supported with all the Cosmos SDK's native functionality. We refer you to their documentation for further information.

### Bitcoin

BitBadges supports Bitcoin P2WPKH addresses and BIP322 message verification.

**Signing Method:** Transactions will be signed in JSON stringified format with all keys alphabetically sorted. JSON messages can also be generated via the SDK.

### Addresses and Public Keys[​](https://docs.injective.network/learn/basic-concepts/accounts#addresses-and-public-keys) <a href="#addresses-and-public-keys" id="addresses-and-public-keys"></a>

#### **Address Conversion**

<pre class="language-typescript"><code class="lang-typescript"><strong>import { ethToBitBadges, bitbadgesToEth } from 'bitbadgesjs-sdk';
</strong>
<strong>const bitbadgesAddress = ethToBitBadges(address);
</strong>const ethAddress = bitbadgesToEth(bitbadgesAddress);
const bitbadgesAddressFromSolana = solanaToBitBadges(address);
//Note there is no bitbadgesToSolana or ethToSolana due to how the addresses work
</code></pre>

Behind the scenes, the blockchain always uses the mapped BitBadges address, never a Solana or Ethereum address. The Solana / Ethereum addresses are only for visually displaying to the user.

The Bech32 format is the default format for Cosmos-SDK queries and transactions through CLI and REST clients.

#### **Representation** <a href="#addresses-and-public-keys" id="addresses-and-public-keys"></a>

Ethereum Example:

* Address (Bech32): `bb14au322k9munkmx5wrchz9q30juf5wjgz2cfqku`
* Address ([EIP55](https://eips.ethereum.org/EIPS/eip-55) Ethereum Hex): `0xAF79152AC5dF276D9A8e1E2E22822f9713474902`

Solana Example:

* Address (Base58): 6H2af68Yyg6j7N4XeQKmkZFocYQgv6yYoU3Xk491efa5
* Address (Bech32): bb18el5ug46umcws58m445ql5scgg2n3tzagfecvl

Bitcoin Example

* Address (Native - P2WPKH): bc1q9s7rynm5pwhluhecsmlku8rn5yej5wdgj0gv3e
* Address (Bech32): bb19s7rynm5pwhluhecsmlku8rn5yej5wdg8g75f9

#### **Public Key Types**

For standard Cosmos accounts, the public key will have the `"@type": "/cosmos.crypto.secp256k1.PubKey"`.

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

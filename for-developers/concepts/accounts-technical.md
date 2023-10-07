# 👥 Accounts (Technical)

## Pre-Readings

* [Cosmos SDK Accounts](https://docs.cosmos.network/main/basics/accounts)
* [Ethereum Accounts](https://ethereum.org/en/whitepaper/#ethereum-accounts)

### BitBadges Accounts[​](https://docs.injective.network/learn/basic-concepts/accounts#injective-accounts) <a href="#injective-accounts" id="injective-accounts"></a>

BitBadges allows Ethereum addresses to use Ethereum's ECDSA secp256k1 curve for keys. The public key for these accounts will be a custom type (provided by [Ethermint](https://github.com/cosmos/ethermint)). This satisfies the [EIP84](https://github.com/ethereum/EIPs/issues/84) for full [BIP44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) paths. The root HD path for BitBadges Ethereum-based accounts is `m/44'/60'/0'/0`. BitBadges uses the Coin type `60` to support Ethereum type accounts, unlike  other Cosmos chains that use Coin type `118.`

Native Cosmos accounts are also supported.&#x20;

### Addresses and Public Keys[​](https://docs.injective.network/learn/basic-concepts/accounts#addresses-and-public-keys) <a href="#addresses-and-public-keys" id="addresses-and-public-keys"></a>

There are 3 main types of `Addresses`/`PubKeys` available by default on Injective:

* Addresses and Keys for **accounts**, which identify users (e.g. the sender of a `message`). They are derived using the **`eth_secp256k1`** curve (Ethermint) or **`secp256k1`** curve (native Cosmos SDK).&#x20;
* Addresses and Keys for **validator operators**, which identify the operators of validators. They are derived using the **`eth_secp256k1`** curve (Ethermint) or **`secp256k1`** curve (native Cosmos SDK).&#x20;
* Addresses and Keys for **consensus nodes**, which identify the validator nodes participating in consensus. They are derived using the **`ed25519`** curve.

|                    | Address bech32 Prefix | Pubkey bech32 Prefix | Curve                       | Address byte length | Pubkey byte length |
| ------------------ | --------------------- | -------------------- | --------------------------- | ------------------- | ------------------ |
| Accounts           | `cosmos`              | `cosmospub`          | `eth_secp256k1 / secp256k1` | `20`                | `33` (compressed)  |
| Validator Operator | `cosmosvaloper`       | `cosmosvaloperpub`   | `eth_secp256k1 / secp256k1` | `20`                | `33` (compressed)  |
| Consensus Nodes    | `cosmosvalcons`       | `cosmosvalconspub`   | `ed25519`                   | `20`                | `32`               |

### Address formats for clients[​](https://docs.injective.network/learn/basic-concepts/accounts#address-formats-for-clients) <a href="#address-formats-for-clients" id="address-formats-for-clients"></a>

For Ethereum accounts and other L1 accounts, they can be represented in both [Bech32](https://en.bitcoin.it/wiki/Bech32) and standard format for compatibility (for Ethereum, this is hex).

The Bech32 format is the default format for Cosmos-SDK queries and transactions through CLI and REST clients.&#x20;

Ethereum Example:

* Address (Bech32): `cosmos14au322k9munkmx5wrchz9q30juf5wjgz2cfqku`
* Address ([EIP55](https://eips.ethereum.org/EIPS/eip-55) Ethereum Hex): `0xAF79152AC5dF276D9A8e1E2E22822f9713474902`
* Compressed Public Key: `{"@type":"/ethermint.crypto.v1.ethsecp256k1.PubKey","key":"AsV5oddeB+hkByIJo/4lZiVUgXTzNfBPKC73cZ4K1YD2"}`

For standard Cosmos accounts, the public key will have the `"@type": "/cosmos.crypto.secp256k1.PubKey"` and will always be represented in Bech32.

**Address Conversion**&#x20;

See [**bitbadgesjs-address-converter**](broken-reference) from the SDK for conversions between different formats.

### Deriving BitBadges Ethereum Account from a private key/mnemonic[​](https://docs.injective.network/learn/basic-concepts/accounts#deriving-injective-account-from-a-private-keymnemonic) <a href="#deriving-injective-account-from-a-private-keymnemonic" id="deriving-injective-account-from-a-private-keymnemonic"></a>

Below you will see an example code snippet on how to derive a BitBadges Account from a private key and/or a mnemonic phase:

```typescript
import { Wallet } from 'ethers'
import { Address as EthereumUtilsAddress } from 'ethereumjs-util'

const mnemonic = "indoor dish desk flag debris potato excuse depart ticket judge file exit"
const privateKey = "afdfd9c3d2095ef696594f6cedcae59e72dcd697e2a7521b1578140422a4f890"
const defaultDerivationPath = "m/44'/60'/0'/0/0"
const defaultBech32Prefix = 'cosmos'
const isPrivateKey: boolean = true /* just for the example */

const wallet = isPrivateKey ? Wallet.fromMnemonic(mnemonic, defaultDerivationPath) : new Wallet(privateKey)
const ethereumAddress = wallet.address
const addressBuffer = EthereumUtilsAddress.fromString(ethereumAddress.toString()).toBuffer()
const cosmosAddress = bech32.encode(defaultBech32Prefix, bech32.toWords(addressBuffer))
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
const type = '/ethermint.crypto.v1.ethsecp256k1.PubKey'
```

#### Acknowledgements

Credit to [https://docs.injective.network/learn/basic-concepts/accounts](https://docs.injective.network/learn/basic-concepts/accounts) and [https://docs.evmos.org/protocol/concepts/accounts](https://docs.evmos.org/protocol/concepts/accounts)

# 👥 Accounts (Low-Level)

## Pre-Readings

* [Cosmos SDK Accounts](https://docs.cosmos.network/main/basics/accounts)
* [Ethereum Accounts](https://ethereum.org/en/whitepaper/#ethereum-accounts)

### Accounts[​](https://docs.injective.network/learn/basic-concepts/accounts#injective-accounts) <a href="#injective-accounts" id="injective-accounts"></a>

For accounts (standard senders of transactions) , we support users from four L1 blockchain ecosystems currently (Ethereum, Bitcoin, Solana, and Cosmos).

### **Ethereum**

BitBadges allows Ethereum addresses to use Ethereum's ECDSA secp256k1 curve for keys. The public key for these accounts will be a custom type (forked from [Ethermint](https://github.com/cosmos/ethermint)). This satisfies the [EIP84](https://github.com/ethereum/EIPs/issues/84) for full [BIP44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) paths. The root HD path for BitBadges Ethereum-based accounts is `m/44'/60'/0'/0`. BitBadges uses the Coin type `60` for Ethereum type accounts, unlike other Cosmos accounts that use Coin type `118.`

**Signing Method:** All transactions should be signed with EIP712. EIP712 transactions can be generated via the BitBadges SDK. An example is provided below.

```
{
    "types": {
        "EIP712Domain": [
            {
                "name": "name",
                "type": "string"
            },
            ....
        ],
        ...
    },
    "primaryType": "Tx",
    "domain": {
        "name": "BitBadges",
        "version": "1.0.0",
        "chainId": 1,
        "verifyingContract": "0xa607FcD07cfe8d84cA839e4D6EdEE4B1A6789603",
        "salt": "0x5d1e2c0e9b8a5c395979525d5f6d5f0c595d5a5c5e5e5b5d5ecd5a5e5d2e5412"
    },
    "message": {
        "chain_id": "bitbadges_1-2",
        "account_number": "12",
        "sequence": "1",
        "fee": {
            "amount": [
                {
                    "amount": "0",
                    "denom": "badge"
                }
            ],
            "gas": "100096"
        },
        "memo": "",
        "msg0": {
            "type": "cosmos-sdk/MsgSend",
            "value": {
                "from_address": "cosmos1kj9kt5y64n5a8677fhjqnmcc24ht2vy97kn7rp",
                "to_address": "cosmos1rgtvs7f82uprnlkdxsadye20mqtgyuj7n4npzz",
                "amount": [
                    {
                        "denom": "badge",
                        "amount": "1"
                    }
                ]
            }
        }
    }
}
```

### **Solana**

BitBadges also extends the SDK's functionality to support Solana signatures signing with a ed25519 key. Addresses are expected to be in the native Base58 format.

**Signing Method:** Transactions will be signed in JSON stringified format with all keys alphabetically sorted. JSON messages can be generated via the SDK.

```
{"account_number":"13","chain_id":"bitbadges_1-2","fee":{"amount":[{"amount":"0","denom":"badge"}],"gas":"100000000000"},"memo":"","msg0":{"type":"cosmos-sdk/MsgSend","value":{"amount":[{"amount":"1","denom":"badge"}],"from_address":"cosmos1zd5dsage58jfrgmsu377pk6w0q5zhc67fn4gsl","to_address":"cosmos1rgtvs7f82uprnlkdxsadye20mqtgyuj7n4npzz"}},"sequence":"0"}
```

### **Cosmos**

Normal Cosmos accounts are also supported with all the Cosmos SDK's native functionality. We refer you to their documentation for further information.

### Bitcoin

BitBadges supports Bitcoin P2WPKH addresses and BIP322 signature verification.

**Signing Method:** Transactions will be signed in JSON stringified format with all keys alphabetically sorted. JSON messages can also be generated via the SDK.

```
{"account_number":"13","chain_id":"bitbadges_1-2","fee":{"amount":[{"amount":"0","denom":"badge"}],"gas":"100000000000"},"memo":"","msg0":{"type":"cosmos-sdk/MsgSend","value":{"amount":[{"amount":"1","denom":"badge"}],"from_address":"cosmos1zd5dsage58jfrgmsu377pk6w0q5zhc67fn4gsl","to_address":"cosmos1rgtvs7f82uprnlkdxsadye20mqtgyuj7n4npzz"}},"sequence":"0"}
```

### **Public Key Types**

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
const type = '/ethereum.PubKey'
```

#### Acknowledgements

Credit to [https://docs.injective.network/learn/basic-concepts/accounts](https://docs.injective.network/learn/basic-concepts/accounts) and [https://docs.evmos.org/protocol/concepts/accounts](https://docs.evmos.org/protocol/concepts/accounts).

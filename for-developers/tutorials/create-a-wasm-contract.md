# Create a CosmWASM Contract

BitBadges support CosmWASM smart contracts to allow you to extend the token interface for custom functionality as desired. However, they are not required at all, and we envision they will only be used in a very, very small percentage of cases. If you do need to extend the interface with unsupported functionality but you think it would be a good fit to be added natively, please let us know. Our end goal is that no smart contracts are ever needed, and everything is supported natively.

#### CosmWASM Version

We currently support

```go.mod
github.com/CosmWasm/wasmd v0.44.0
```

**No tx.origin**

Note that Cosmos SDK / CosmWasm does not have a Solidity **tx.origin** equivalent. **All Cosmos Msgs called from the contract have Msg.Creator equal to the CONTRACT ADDRESS, not the calling user's address.**

This may not be ideal, but it is what it is for security reasons. You may need to come up with creative workarounds or creative solutions in certain situations.&#x20;

## Tutorial

This tutorial assumes some understanding of Rust and CosmWASM. Both these topics are heavily documented, so if something is confusing, please refer to the corresponding documentation (such as [https://cosmwasm.com/build/](https://cosmwasm.com/build/) or [https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)).

Please use and reference this library [here ](https://github.com/BitBadges/bitbadges-cosmwasm-bindings/tree/master/contracts/register\_addresses)(bitbadges-cosmwasm-bindings) for contract examples, types, and Rust bindings throughout this tutorial.

**NOTE:** The BitBadges bindings from the above repository do not cover messages from pre-written modules that have already been implemented by the CosmWasm team, such as staking-related messages and fundamental ones like `MsgSend`. See their documentation if you want to interact with other pre-written modules as well. For this tutorial, we will focus on interacting with the x/badges module only.

It is always recommended that you test everything on a testnet before deploying for real.

### **Step 1: Create Contract**

Create your contract.

The recommended way is to clone the bitbadges cosmwasm repository and create your contracts within the contract folder (recommended). Use the preexisting contracts as a reference.

Or, you can custom implement from scratch. If you want to develop in your own directory (i.e. not cloning the repo above), note that the bitbadges-cosmwasm import in Cargo.toml is currently a local path import. You will need to import the published package version (see [bitbadges-cosmwasm](https://crates.io/crates/bitbadges-cosmwasm)).

```toml
[dependencies]
bitbadges-cosmwasm = { version = "X.X.X" }
```

**Creating the Contract**

We leave the actual logic of your contract for you to handle. Please view examples or refer to CosmWASM documentation if needed.

**Building and Optimizing**

Once you have your contract, you can run **cargo build** and **source ./build.sh** to get a .wasm compiled version of your contract. Note that ./build.sh may need to be edited for your new contract if you cloned the sample repo.

You can also use an optimizer instead of **build.sh** to further optimize your bytecode size. One example optimizer is [https://github.com/CosmWasm/rust-optimizer](https://github.com/CosmWasm/rust-optimizer).

At the end of Step 1, you should have a compiled .wasm file.

### **Step 2: Storing and Instantiating the Contract**

This step assumes that you are running a node and have familiarity with the CLI. You can also check out other tools such as [https://github.com/cryptechdev/wasm-deploy](https://github.com/cryptechdev/wasm-deploy) to deploy without running a node (note this is untested).

We do export the x/wasm Msg types from the SDK, but we haven't created a tutorial yet.

```
bitbadgeschaind tx wasm store /path/to/contract.wasm --from=<address> --chain-id=<chain-id> --gas=auto -y
```

You will then need to get the code\_id from the transaction. This will be in the raw\_log of the returned output from the above command. You can also query wasm module to find your code ID.

```
raw_log: '[....{"key":"code_id","value":"4"}....]'
```

Then, you instantiate the contract via:

```
bitbadgeschaind tx wasm instantiate code_id_from_above {} --label "your label" --no-admin --from=<address> --gas=auto -y
```

This will then return a contract address in the raw\_log output. Or, you can query the contract address by code\_id using the wasm query commands.

```
raw_log: '[....{"key":"_contract_address","value":"cosmos1ghd753shjuwexxywmgs4xz7x2q732vcnkm6h2pyv9s6ah3hylvrqa0dr5q"}....]'
```

Your contract is now deployed on the blockchain and ready to be interacted with.

**Step 3: Interacting with the Contract**

Once deployed, you need to interact with the contract somehow or let your users interact with it. As explained, there is a MsgExecuteContract (standard out of the box one compatible for Cosmos users) and a MsgExecuteContractCompat (wrapper that allows Ethereum users w/ EIP712 or Solana to interact with it). MsgExecuteContractCompat simply is a helper Msg that parses everything in a compatible manner and then calls the actual **MsgExecuteContract** We recommend using MsgExecuteContractCompat. This is the same as broadcasting any other transaction, so we refer you to [Creating, Signing, and Broadcasting Txs](../create-and-broadcast-txs/) for a tutorial.

**Couple Notes**

The expected **msg and funds** field should be in the following format

```
msg: '{"deleteCollectionMsg": {"collectionId": "1"}}',
funds: '1badge'
```

Note that even though snake\_case may be used in the contracts / Rust, the badge modules use camelCase as seen in the command above.

The msg details should be in the format where only one Msg is defined. All numbers should be strings as well ("1" not 1). The creator field is auto-populated with the contract address.

```json
{
    "deleteCollectionMsg": {...}
    "createAddressListsMsg": {...},
    "updateCollectionMsg": {...},
    "updateUserApprovedTransfersMsg": {...},
    "transferBadgesMsg": {...},
    "createCollectionMsg": {...},
    "universalUpdateCollectionMsg": {...}
}
```

For example,

```json5
{
    "deleteCollectionMsg": {"collectionId": "1"}
}
```

**Example**

Note replace with values for your contract. **msg** should be a JSON encoded string with the details of your function call.

```typescript
import { MessageMsgExecuteContractCompat, createTxMsgExecuteContractCompat } from 'bitbadgesjs-transactions';

const msgExecuteContract: MessageMsgExecuteContractCompat = {
    sender: "cosmos1uqxan5ch2ulhkjrgmre90rr923932w38tn33gu", //enter sender adress here
    contract: "cosmos14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9s4hmalr", // 
    msg: '{"deleteCollectionMsg": {"collectionId": "1"}}',
    funds: '1badge'
}
const executeTx = await createTxMsgExecuteContractCompat(...txDetails, msgExecuteContract);

```

**Step 4: Build a dApp Frontend**

Once you have done the previous three steps, you can build a dApp frontend, so users can interact with your contract!

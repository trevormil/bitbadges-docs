# Create a CosmWASM Contract

BitBadges support CosmWASM smart contracts to allow you to extend the token interface for custom functionality as desired. However, they are not required at all, and we envision they will only be used in a very, very small percentage of cases.&#x20;

If you do need to extend the interface with unsupported functionality but you think it would be a good fit to be added natively, please let us know. Our end goal is that no smart contracts are ever needed, and everything is supported natively!

#### CosmWASM Version

```go.mod
github.com/CosmWasm/wasmd v0.44.0
```

## CosmWasm - High-Level Overview

This is just a high-level overview to familiarize you with some of the main concepts of CosmWASM. Please refer to their official documentation for more thorough documentation.

**Store and Instantiate**

The first step is to store and instantiate your contract. We walk you through this in the tutorial below.

**Contract Addresses**

Every contract will have a Cosmos bech32 contract address (e.g. cosmos14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9s4hmalr).

**Execute**

The main execution part of every contract is ExecuteMsg. This is what users can call to interact. Your execute function will look similar to the following. They will interact via MsgExecuteContractCompat such as below.&#x20;

The **msg** field in MsgExecuteContractCompat should be a JSON string compatible with the **ExecuteMsg** of your contract. Take note of single vs double quotes. Also, note whether you serialize to camelCase or another format.

If you want to execute with funds, the **funds** property will be in the format "1badge".&#x20;

```typescript
const msgExecuteContract: MsgExecuteContractCompat = {
    sender: "cosmos1uqxan5ch2ulhkjrgmre90rr923932w38tn33gu", //enter sender adress here
    contract: "cosmos14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9s4hmalr", // 
    msg: '{"deleteCollectionMsg": {"collectionId": "1"}}',
    funds: '1badge'
}
```

```rust
//contract.rs
#[entry_point]
pub fn execute(
    _deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response<BitBadgesMsg>, StdError> {
    match msg {
        ExecuteMsg::TransferBadgeMsg { collection_id, transfers } => {
          execute_msg_transfer_badges(collection_id, transfers)
        }
        // Add other messages here as needed
    }
}
```

```rust
//msg.rs
use schemars::JsonSchema;
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize, JsonSchema)]
pub struct InstantiateMsg {}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "camelCase")]
pub enum ExecuteMsg {
    #[serde(rename_all = "camelCase")]
    DeleteCollectionMsg {
        collection_id: String,
    }
}
```

**No tx.origin**

Note that Cosmos SDK / CosmWasm does not have a Solidity **tx.origin** equivalent. This may not be ideal, but it is what it is for security reasons. You may need to come up with creative workarounds or creative solutions in certain situations.&#x20;

## **BitBadges Bindings Repository**

Please use and reference this library [here ](https://github.com/BitBadges/bitbadges-cosmwasm-bindings/tree/master/contracts/register\_addresses)(bitbadges-cosmwasm-bindings) for contract examples, types, and Rust bindings throughout this tutorial.

**NOTE:** The BitBadges bindings from the above repository do not cover messages from pre-written modules that have already been implemented by the CosmWasm team, such as staking-related messages and fundamental ones like `MsgSend`. See their documentation if you want to interact with other pre-written modules as well. For this tutorial, we will focus on interacting with the x/badges module only.

This repository exports the types and Msg functions which can be used as

```rust
use bitbadges_cosmwasm::{
  Transfer, Balance, ....
};
```

```rust
use bitbadges_cosmwasm::{
  transfer_badges_msg, delete_collection_msg, BitBadgesMsg
}
```

```rust
pub fn execute_msg_transfer_badges(
    collection_id: String,
    transfers: Vec<Transfer>,
) -> StdResult<Response<BitBadgesMsg>> {
    let msg = transfer_badges_msg(
        collection_id,
        transfers,
    );

    Ok(Response::new().add_message(msg))
}

#[entry_point]
pub fn execute(
    _deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response<BitBadgesMsg>, StdError> {
    match msg {
        ExecuteMsg::TransferBadgeMsg { collection_id, transfers } => {
          execute_msg_transfer_badges(collection_id, transfers)
        }
        // Add other messages here as needed
    }
}
```

## Tutorial

This tutorial assumes some understanding of Rust and CosmWASM. Both these topics are heavily documented, so if something is confusing, please refer to the corresponding documentation (such as [https://cosmwasm.com/build/](https://cosmwasm.com/build/) or [https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)).

It is always recommended that you test everything on a testnet before deploying for real.

### **Step 1: Create Contract**

Create your contract.

The recommended way is to clone the bitbadges cosmwasm repository and create your contracts within the contracts folder (recommended). Use the preexisting contracts as a reference.

Or, you can custom implement from scratch. If you want to develop in your own directory (i.e. not cloning the repo above), you will need to import the published package version (see [bitbadges-cosmwasm](https://crates.io/crates/bitbadges-cosmwasm)).

```toml
[dependencies]
bitbadges-cosmwasm = { version = "X.X.X" }
```

**Creating the Contract**

We leave the actual logic of your contract for you to handle. Please reference the explanations above, view examples, or refer to CosmWASM documentation if needed ([https://github.com/BitBadges/bitbadges-cosmwasm-bindings/tree/master/contracts/register\_addresses/src](https://github.com/BitBadges/bitbadges-cosmwasm-bindings/tree/master/contracts/register\_addresses/src)).

**Building and Optimizing**

Once you have your contract, you can run **cargo build** and **source ./build.sh** to get a .wasm compiled version of your contract (assuming you cloned the repo, or else, do this step manually). Note that ./build.sh may need to be edited for your contract name if you cloned the sample repo and potentially the filepath.

build.sh

```bash
RUSTFLAGS='-C link-arg=-s' cargo wasm
cp ../../target/wasm32-unknown-unknown/release/YOUR_CONTRACT_NAME.wasm .
```

You can also use an optimizer instead of **build.sh** to further optimize your bytecode size. One example optimizer is [https://github.com/CosmWasm/rust-optimizer](https://github.com/CosmWasm/rust-optimizer).

However you choose to complete this step, at the end, you should have a compiled .wasm file.

### **Step 2: Storing and Instantiating the Contract**

The easiest way to do this is simply through the helper interface we have created. You can also do it via interacting with the x/wasm or x/wasmx modules via the CLI, but the interface is a lot more streamlined and has support for all supported chains (CLI only supports Cosmos signatures).

1\) gzip your .wasm file to obtain a .wasm.gz file

```
gzip contract.wasm
```

2\) Go to [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast) and select MsgStoreCodeCompat. Upload your .wasm.gz file. Submit the transaction.

3\) A notification should pop up with your code ID, assuming the transaction was successful.

4\) Refresh the page or clear all Msgs and start a new transactioon. Now, select MsgInstantiateContractCompat. Set a label (name for your contract) and enter the code ID from step 3. Submit the transaction.

If you want to instantiate it with funds. the **funds** property will be in the format "1badge".

5\) A notification should pop up with your contract's address. Store this somewhere.&#x20;

Your contract is now deployed on the blockchain and ready to be interacted with.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Step 3: Interacting with the Contract**

Once deployed, you need to to let your users interact with it.&#x20;

This can be done with MsgExecuteContractCompat. MsgExecuteContractCompat simply is a helper Msg that parses everything in a compatible manner (support for users from all supported chains rather than just Cosmos) and then calls the actual CosmWasm's **MsgExecuteContract.** This is the same as broadcasting any other transaction, so we refer you to [Creating, Signing, and Broadcasting Txs](../create-and-broadcast-txs/) for a tutorial.

Consider building a dApp frontend, so users can easily interact with your contract! Use [https://bitbadges.io/dev/dappexample](https://bitbadges.io/dev/dappexample) ([Code](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/pages/dev/dappexample.tsx)) as a reference.

**Example**

```typescript
const msgExecuteContract: MsgExecuteContractCompat = {
    sender: "cosmos1uqxan5ch2ulhkjrgmre90rr923932w38tn33gu", //enter sender adress here
    contract: "cosmos14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9s4hmalr", // 
    msg: '{"deleteCollectionMsg": {"collectionId": "1"}}',
    funds: '1badge'
}
```


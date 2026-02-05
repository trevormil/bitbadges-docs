# Create a WASM Contract

BitBadges support CosmWASM smart contracts to allow you to extend the token interface for custom functionality as desired. If you do need to extend the interface with unsupported functionality but you think it would be a good fit to be added natively, please let us know. Our end goal is that no smart contracts are ever needed, and everything is supported natively!

We refer you to official CosmWASM and Rust documentation for more information. This tutorial will only focus on BItBadges specific information.

{% embed url="https://cosmwasm.cosmos.network/" %}

```go.mod
github.com/CosmWasm/wasmd v0.52.0
```

**Permissioned Uploads**

```
BitBadges implements CosmWASM in a permissioned manner.
```

Instantiating and officially deploying a contract does require a review process and governance approval. Reach out for more information.

Requirements:

* No avoidance of the protocol fee. Any token transfers that take place must use MsgTransferTokens, or if you need to implement transfer functionality directly in the contract, the protocol fee must be obeyed.
* We encourage audits and a peer review process before officailly deploying.
* You must also showcase a working testnet implementation as well (testnet is permissionless)

Generally speaking, even though this is a permissioned implementation, we want the contract layer to be as permissionless as possible. We just have to enforce that our business model (protocol fee) is obeyed.

**High-Level Development Overview**

```rust
const msgExecuteContract: MsgExecuteContractCompat = {
    sender: 'bb1uqxan5ch2ulhkjrgmre90rr923932w38gwez5d',
    contract:
        'bb14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sc8kg9e',
    msg: '{"myCustomMsg1": {"collectionId": "1"}}',
    funds: '1ubadge',
};
```

* Each contract has a Cosmos bech32 contract address (e.g. bb14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sc8kg9e).
* 3-Step Process: Upload / Store Code, Instantiate, Execute
  * Instantiating requires governance proposal (see below)
* Contracts can call into the core x/tokenization module as submessages (delayed until directly after the contract)
  * When calling into x/tokenization, msg.Creator will ALWAYS be the contract address. You may need to come up with creative workarounds or creative solutions in certain situations.
* The main execution part of every contract is ExecuteMsg. Think of this like the API definition for your contract. You can have multiple message types with different logic.

```rust
//contract.rs
use bitbadges_cosmwasm::{
  transfer_tokens_msg
};

#[entry_point]
pub fn execute(
    _deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response<BitBadgesMsg>, StdError> {
    match msg {
        ExecuteMsg::CustomMsg1 { collection_id, transfers } => {
          let msg = transfer_tokens_msg(collection_id, transfers);
          Ok(Response::new().add_submessage(SubMsg::reply_always(msg, BADGES_REPLY_ID)))
        }
        ExecuteMsg::CustomMsg2 { collection_id, transfers } => {
          let msg = transfer_tokens_msg(collection_id, transfers);
          Ok(Response::new().add_submessage(SubMsg::reply_always(msg, BADGES_REPLY_ID)))
        }
        // Add other messages here as needed
    }
}
```

### **Create a Contract Tutorial**

1. Clone the BitBadges Cosmwasm repository

```
git clone https://github.com/BitBadges/bitbadges-cosmwasm-bindings.git
cd bitbadges-cosmwasm-bindings
```

This repository exports all the custom bindings via `packages/bitbadges-cosmwasm`. You should typically NOT edit the exported types. These are already pregenerated exactly as defined on-chain for you to use.

2. Create your contract

You will be dealing with the `contracts` folder. This is where you implement your contract logic. The `contracts` folder is automatically configured to import from the local `packages/bitbadges-cosmwasm.` We have provided an example and boilerplate for you.

Tools at your disposal:

```rust
// All BitBadges type bindings
use bitbadges_cosmwasm::{
  Transfer, Balance, ....
};
```

<pre class="language-rust"><code class="lang-rust">// BitBadges *_msg() functions
// This is how you call into x/tokenization by adding Ok(Response::new().add_message(msg))
use bitbadges_cosmwasm::{
  transfer_tokens_msg, delete_collection_msg, BitBadgesMsg
}

<strong>pub fn execute_my_msg(
</strong>    collection_id: String,
    transfers: Vec&#x3C;Transfer>,
) -> StdResult&#x3C;Response&#x3C;BitBadgesMsg>> {
    let msg = transfer_tokens_msg(
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
) -> Result&#x3C;Response&#x3C;BitBadgesMsg>, StdError> {
    match msg {
        ExecuteMsg::MyCustomMsg { collection_id, transfers } => {
          execute_my_msg(collection_id, transfers)
        }
        // Add other messages here as needed
    }
}
</code></pre>

3. Build and Optimize

You may have to edit the script for you file names and paths. Consider also using an optimizer like [https://github.com/CosmWasm/rust-optimizer](https://github.com/CosmWasm/rust-optimizer).

```bash
cargo build
source ./build.sh # outputs a .wasm
gzip youcontractname.wasm # outputs a .wasm.gz
```

```bash
# build.sh
RUSTFLAGS='-C link-arg=-s' cargo wasm
cp ../../target/wasm32-unknown-unknown/release/YOUR_CONTRACT_NAME.wasm .
```

4. Uploading Your Contract

Go to [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast) and select MsgStoreCode. Upload your .wasm.gz file. Submit the transaction.

For testnet uploads, use testnet.bitbadges.io

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

5. Instantiate your Contract

A notification from Step 4 should pop up with your code ID, assuming the transaction was successful.

Refresh the page or clear all Msgs and start a new transaction. Now, select MsgInstantiateContractCompat. Set a label (name for your contract) and enter the code ID from step 3. Submit the transaction.

If you want to instantiate it with funds. the **funds** property will be in the format "1badge".

A notification should pop up with your contract's address. Store this somewhere.

```
Note: This is only applicable to testnet.

For mainnet, instantiation requires a governance proposal. Reach out to us to start this process.
```

6. Interact with the Contract

Once deployed, you need to to let your users interact with it. Use our custom x/wasmx MsgExecuteContractCompat to do so. This is a wrapper around the core x/wasm MsgExecuteContract with support for BitBadges signing. This is the same as broadcasting any other transaction, so we refer you to Creating, Signing, and Broadcasting Txs for a tutorial.

Couple common misunderstandings:

* Note msg is an encoded stringified JSON. Be mindful of single vs double quotes and escaped characters.
*   Note camelCase vs snake\_case. Contracts typically auto-format with camelCase via

    ```rust
    #[serde(rename_all = "camelCase")]
    ```
* Parsing may not be smart enough to identify empty values like empty arrays. You may have to manually specify empty strings, arrays for compatibility.

**Examples**

```typescript
const msgExecuteContract: MsgExecuteContractCompat = {
    sender: 'bb1uqxan5ch2ulhkjrgmre90rr923932w38gwez5d', //enter sender adress here
    contract: 'bb14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sc8kg9e', //
    msg: '{"deleteCollectionMsg": {"collectionId": "1"}}',
    funds: '1badge',
};
```

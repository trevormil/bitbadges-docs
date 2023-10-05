# Create a Smart Contract

BitBadges support CosmWASM smart contracts to allow you to extend the token interface for custom functionality as desired. However, they are not required.&#x20;

If you do need to extend the interface with unsupported functionality but you think it would be a good fit to be added natively, please let us know

#### CosmWASM Version

For compatibility with the most recent Cosmos SDK versions, we have used a fork of the main CosmWasm code created by Notional Labs ([https://github.com/notional-labs/wasmd](https://github.com/notional-labs/wasmd)).&#x20;

**No tx.origin**

Note that Cosmos SDK / CosmWasm does not have a Solidity **tx.origin** equivalent. **All Cosmos Msgs called from the contract have Msg.Creator equal to the CONTRACT ADDRESS, not the calling user's address.**

This may not be ideal, but it is what it is for security reasons. You may need to come up with creative workarounds or creative solutions in certain situations. For example,

\-Give the contract the manager role

\-Have users approve the contract to transfer on their behalf and so on

## Tutorial

This tutorial assumes some understanding of Rust and CosmWASM. Both these topics are heavily documented, so if something is confusing, please refer to the corresponding documentation (such as [https://cosmwasm.com/build/](https://cosmwasm.com/build/) or [https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)).

Please use and reference this library [here ](https://github.com/BitBadges/bitbadges-cosmwasm-bindings/tree/master/contracts/register\_addresses)(bitbadges-cosmwasm-bindings) for contract examples, types, and Rust bindings throughout this tutorial.&#x20;

**NOTE:** The BitBadges bindings from the above repository do not cover messages from pre-written modules that have already been implemented by the CosmWasm team, such as staking-related messages and fundamental ones like `MsgSend`. See their documentation if you want to interact with other pre-written modules as well. For this tutorial, we will focus on interacting with the x/badges module only.



It is always recommended that you test everything on a testnet before deploying for real.

### **Step 1: Create Contract**

Create your contract.

The recommended way is to clone the bitbadges repository and create your contracts within the contract folder (recommended).&#x20;

Or, you can custom implement from scratch. If you want to develop in your own directory (i.e. not cloning the repo above), note that the bitbadges-cosmwasm import in Cargo.toml is currently a local path import. You will need to import the published package version (see [bitbadges-cosmwasm](https://crates.io/crates/bitbadges-cosmwasm)).

**Creating the Contract**

We leave the actual logic of your contract for you to handle. Please view examples or refer to CosmWASM documentation if needed.

**Building and Optimizing**

Once you have your contract, you can run **cargo build** and **source ./build.sh** to get a .wasm compiled version of your contract. Note that ./build.sh may need to be edited for your new contract if you cloned the sample repo.

You can also use an optimizer instead of **build.sh** to further optimize your bytecode size. One example optimizer is [https://github.com/CosmWasm/rust-optimizer](https://github.com/CosmWasm/rust-optimizer).



At the end of Step 1, you should have a compiled .wasm file.

### **Step 2: Storing and Instantiating the Contract**

This step assumes that you are running a node and have familiarity with the CLI. You can also check out other tools such as [https://github.com/cryptechdev/wasm-deploy](https://github.com/cryptechdev/wasm-deploy) to deploy without running a node (note this is untested). We plan to add it to BitBadges SDK and create a simple user interface for deployment in the future (or this could be an idea for a community-built tool).

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

Once deployed, you need to interact with the contract somehow or let your users interact with it. As explained, there is a MsgExecuteContract (standard out of the box one compatible for Cosmos susers) and a MsgExecuteContractCompat (wrapper that allows Ethereum users w/ EIP712 to interact with it). MsgExecuteContractCompat simply is  a helper Msg that parses everything in a compatible manner and then calls the actual **MsgExecuteContract** We recommend using MsgExecuteContractCompat.



**Option 1: CLI**

Finally, you can interact with your contract via the execute command. The example below is for registering addresses using the MsgRegisterAddresses command.

```
bitbadgeschaind tx wasm execute contract_address '......' --from=<address> --gas=auto -y
```

Note that even though snake\_case may be used in the contracts / Rust, the badge modules use camelCase as seen in the command above.

The MsgExecuteContractCompat is in the x/wasmx module so would be executable by "bitbadgeschaind tx wasmx ..." but if you are using the CLI, you most likely are using a Cosmos address so can probably interact with the former command.



**Option 2: BitBadges SDK**

The BitBadges SDK offers a [**createTxMsgExecuteContractCompat**](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/functions/createMsgExecuteContractCompat.html) function which allows you to create, sign, and broadcast an execute contract Msg within JavaScript (see [Broadcasting Txs](../../sdk/broadcasting-and-signing-txs.md)) compatible with Ethereum EIP712. This allows you to create dApps and a frontend for your contract for any user.

For users to execute your contract (potentially via a dApp), we recommend using the MsgExecuteContractCompat instead of the base MsgExecuteContract. This is the same as braodcasting a transaction with any other Msg type, so we refer you [here](../../sdk/common-snippets/creating-signing-and-broadcasting-txs.md) for further details.

**Example**

Note replace with values for your contract. **msg** should be a JSON encoded string with the details of your function call.

```typescript
import { MessageMsgExecuteContractCompat, createTxMsgExecuteContractCompat } from 'bitbadgesjs-transactions';

const msgExecuteContract: MessageMsgExecuteContractCompat = {
    sender: "cosmos1uqxan5ch2ulhkjrgmre90rr923932w38tn33gu", //enter sender adress here
    contract: "cosmos14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9s4hmalr", // 
    msg: '{...msg details}',
    funds: '1badge'
}
const executeTx = await createTxMsgExecuteContractCompat(...txDetails, msgExecuteContract);

```

**Step 4: Build a dApp**

Once you have done the previous three steps, you can build a dApp frontend, so users can interact with your contract! See [Build a dApp](build-a-dapp.md).

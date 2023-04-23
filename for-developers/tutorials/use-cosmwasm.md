# Use CosmWasm

CosmWasm Version: For compatibility with the most recent Cosmos SDK versions, we use a fork of the main CosmWasm code created by Notional Labs ([https://github.com/notional-labs/wasmd](https://github.com/notional-labs/wasmd)).&#x20;

IMPORTANT: Note that when CosmWasm interacts with the badges module (i.e. creates and broadcasts a [Tx Msg](../need-to-know/tx-msg-interfaces.md)), the creator field (calling address) of the Msg will always be the contract's address / account ID number, NOT THE ORIGINAL USER's address / account ID number (this will automatically be applied by the blockchain) In other words, Cosmos SDK / CosmWasm does not have a Solidity **tx.origin** equivalent.

For example, if you want to create a function which updates the permissions of a badge collection according to some logic, this would only be possible if the contract is the manager. It would not work if the original user calling the contract is the manager because technically, the contract submitted the Msg, not the original user. Thus, the badge collection's permissions will not allow it.&#x20;



Some common workarounds around include:

\-Give the contract the manager role

\-Have users approve the contract to transfer w/ MsgSetApproval

### Tutorial

This tutorial assumes some understanding of Rust and CosmWASM. Both these topics are heavily documented, so if something is confusing, please refer to the corresponding documentation (such as [https://cosmwasm.com/build/](https://cosmwasm.com/build/) or [https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)).



It is recommeded you test everything on a testnet before deploying for real.

**Step 1: Create Contract**

Create your contract. Examples can be found at [https://github.com/bitbadges/bitbadges-cosmwasm-bindings/contracts](https://github.com/bitbadges/bitbadges-cosmwasm-bindings/contracts) for interacting with the BitBadges badge module.&#x20;

**NOTE:** The BitBadges bindings from the above repository do not cover messages that have already been implemented by the CosmWasm team, such as staking-related messages and fundamental ones like `MsgSend`. See corresponding documentation if you want to interact with other modules as well. For this tutorial, we will focus on interacting with the badges module only.



You can choose to clone this repository and create your contracts within the contract folder (recommended). Clone one of the example contracts and edit build.sh with your contract name.

Then, you can run **cargo build** and **source ./build.sh** to get a .wasm compiled version of your contract.&#x20;

Note that you can also use an optimizer instead of **build.sh** to further optimize your bytecode size. One example optimizer is [https://github.com/CosmWasm/rust-optimizer](https://github.com/CosmWasm/rust-optimizer).



If you want to develop in your own directory (i.e. not cloning the repo above), note that the bitbadges-cosmwasm import in Cargo.toml for these contracts is a local path import. You can import the published package version from **TODO**.



At the end of Step 1, you should have a compiled .wasm file.

**Step 2: Storing and Instantiating the Contract**

This step assumes that you are running a node and have familiarity with the CLI. You can also check out other tools such as [https://github.com/cryptechdev/wasm-deploy](https://github.com/cryptechdev/wasm-deploy) to deploy without running a node (note this is untested). We plan to create a simple user interface for deployment in the future (or this could be an idea for a community-built tool).

```
bitbadgeschaind tx wasm store /path/to/contract.wasm --from=<address> --chain-id=<chain-id> --gas=auto -y
```

You will then need to get the code\_id. This will be in the raw\_log of the returned output from the above command. You can also use the query wasm commands to find your code ID.

```
raw_log: '[....{"key":"code_id","value":"4"}....]'
```

Then, you instantiate the contract via:

```
bitbadgeschaind tx  wasm instantiate code_id_from_above {} --label "your label" --no-admin --from=<address> --gas=auto -y
```

This will then return a contract address in the raw\_log output. Or, you can query the contract address by code\_id using the wasm query commands.

```
raw_log: '[....{"key":"_contract_address","value":"cosmos1ghd753shjuwexxywmgs4xz7x2q732vcnkm6h2pyv9s6ah3hylvrqa0dr5q"}....]'
```



Your contract is now deployed on the blockchain and ready to be interacted with.

**Step 3: Interacting with the Contract**

Finally, you can interact with your contract via the execute command. The example below is for registering addresses using the MsgRegisterAddresses command.

```
bitbadgeschaind tx wasm execute contract_address '{"registerAddressesMsg": {"addressesToRegister" :["cosmos1hsk6jryyqjfhp5dhc55tc9jtckygx0eph6dd02"] }}' --from=<address> --gas=auto -y
```

Note that even though snake\_case may be used in the contracts / Rust, the badge modules use camelCase as seen in the command above.

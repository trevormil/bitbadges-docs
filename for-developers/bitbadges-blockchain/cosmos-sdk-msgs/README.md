# Cosmos SDK Msgs

**What are Cosmos SDK Msgs? Msgs vs Transactions?**

In Cosmos SDK, Msgs are messages that represent actions to be executed on the blockchain, such as sending tokens.

Each transaction must consist of one or Msgs to be executed. Transactions also consist of other accompanying details such as the signature info.

**What Msgs does the BitBadges blockchain implement?**

The BitBadges blockchain utilizes various pre-written modules from the Cosmos SDK (auth, authz, genutil, bank, capability, staking, distr, gov, params, crisis, slashing, feegrant, group, wasm, ibc, upgrade, evidence, transfer, ica, vesting). The documentation for the pre-written modules can be found [here](https://docs.cosmos.network/main/modules).

The x/badges module is the core functionality of BitBadges written by us, and within this module, all the Msg types that correspond to tokens are defined. We also use an x/wasmx module which helps to create compatible smart contracts (forked from Injective). The x/maps allows storing data in a structured format with many customization options for the map. The x/anchor alllows for storing unstructured data.

**How to broadcast transactions with Msgs?**

You can generate and submit your transactions (Msgs) via:

* [BitBadges SDK](../create-and-broadcast-txs/): Generate and broadcast transactions to a running node with TypeScript. See [tutorials](../create-and-broadcast-txs/). This is the recommended option.
* CLI: Run your own node and interact with the command line
* Other: [https://docs.cosmos.network/main/user/run-node/txs#using-rest](https://docs.cosmos.network/main/user/run-node/txs#using-rest)

**What is the creator field?**

The **creator** field for each message should be the transaction signer's BitBadges address.

## Msg Definitions

Below, we link the documentation for the Msgs from our x/badges and x/wasmx module.

**x/badges**

* [MsgCreateCollection](https://bitbadges.github.io/bitbadgesjs/classes/MsgCreateCollection.html) - Creates a new collection. For creation transactions, everything is considered "free" (no permission restrictions). For following update transactions, everything must follow the permissions set.
* [MsgUpdateCollection](https://bitbadges.github.io/bitbadgesjs/classes/MsgUpdateCollection.html) - Updates the details of a collection. Must be manager of the corresponding collection to execute and all updates must follow the permissions set.
* [MsgUniversalUpdateCollection](https://bitbadges.github.io/bitbadgesjs/classes/MsgUniversalUpdateCollection.html) - This is a universal all-in-one message that supports everything from both MsgCreateCollection and MsgUpdateCollection. If collectionId == 0, we treat it as a create transaction. If collectionId > 0, we update the corresponding collection.
  * Mainly used for legacy purposes. To avoid confusion, we recommend using MsgCreate or MsgUpdate because those will be typed correctly for your use case.
* [MsgTransferTokens](https://bitbadges.github.io/bitbadgesjs/classes/MsgTransferTokens.html) - Transfer tokens between users, if approvals allow.
* [MsgUpdateUserApprov](https://bitbadges.github.io/bitbadgesjs/classes/MsgUpdateUserApprovals.html)[als](https://bitbadges.github.io/bitbadgesjs/classes/MsgUpdateUserApprovals.html) - Set incoming / outgoing approvals for a collection, in addition to permissions which define the updatability of the approvals.
* [MsgDeleteCollection](https://bitbadges.github.io/bitbadgesjs/classes/MsgDeleteCollection.html) - Deletes the collection, if permissions allow. Must be manager.
* [MsgCreateAddressLists](https://bitbadges.github.io/bitbadgesjs/classes/MsgCreateAddressLists.html) - Creates address list(s).

**x/wasmx**

* [MsgExecuteContractCompat](https://bitbadges.github.io/bitbadgesjs/classes/MsgExecuteContractCompat.html) - Helper Msg to support executing contracts with support for BitBadges special transaction signing. See [here](../concepts/accounts-technical.md) for tutorial.

**x/maps**

* MsgCreateMap - Creates a map, uniquely identifed by an ID
* MsgUpdateMap - Updates an existing map.
* MsgDeleteMap - Deletes a map
* MsgSetValue - Allows a user to specify a (key, value) pair if permissions allow.

**x/anchor**

* MsgAddCustomData - Add custom data to the blockchain. No structure to the data at all (just a string). Will return a location for where to find your data.

**Other Cosmos SDK Modules**

For other standard Cosmos SDK messages, you can check out the bitbadges SDK documentation (such as [MsgSend](https://bitbadges.github.io/bitbadgesjs/classes/MsgSend.html) here). Or, check the official Cosmos documentation as these were written by them!

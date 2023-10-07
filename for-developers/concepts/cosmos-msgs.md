# ✉ Cosmos Msgs

**What are Cosmos SDK Msgs?**

In Cosmos SDK, Msgs are messages that represent actions to be executed on the blockchain, such as sending tokens. Each transaction must consist of one or Msgs to be executed.

Each Cosmos SDK module can define their own Msg types based on their specific functionality. For example, the staking module defines Msgs for actions related to staking, such as creating a validator or delegating tokens. The governance module defines Msgs for submitting and voting on proposals to change the parameters of the blockchain. This modular approach allows developers to add and remove functionality as needed, and enables interoperability between different blockchains that use the Cosmos SDK.&#x20;

**What Msgs does the BitBadges blockchain implement?**

The BitBadges blockchain utilizes various pre-written modules from the Cosmos SDK (auth, authz, genutil, bank, capability, staking, distr, gov, params, crisis, slashing, feegrant, group, wasm, ibc, upgrade, evidence, transfer, ica, vesting). The documentation for the pre-written modules can be found [here](https://docs.cosmos.network/main/modules).&#x20;

However, the x/badges module is the core functionality of BitBadges written by us, and within this module, all the Msg types that correspond to badges are defined. We also use an x/wasmx module which helps to create compatible smart contracts.

Some common Msgs from pre-written modules you may also need include:

* MsgSend from the x/bank module - Send $BADGE
* MsgDelegate and MsgUndelegate from the x/staking module - Stake / Unstake $BADGE

**How to broadcast transactions with Msgs?**

You can create / submit / broadcast your transactions (Msgs) to the BitBadges team's official node via [the BitBadges SDK](../../sdk/broadcasting-and-signing-txs.md). This is the recommended option. You do not have to run a node and only need to ever use TypeScript. Check out the tutorials in the tutorials section.

Using the above tutorial is the recommended method, but you can also interact with other nodes (i.e. one you run yourself or other BitBadges nodes). See [https://docs.cosmos.network/main/run-node/interact-node](https://docs.cosmos.network/main/run-node/interact-node) and [https://docs.cosmos.network/main/run-node/txs](https://docs.cosmos.network/main/run-node/txs) for your options. There are plenty of resources out there for interacting with a Cosmos SDK node.

### Notes

* The **creator** field for each message should be the transaction sender's cosmos address. This is automatically applied by the blockchain but should match when creating each Msg.

## Msg Types

Below, we link the documentation for the Msgs from our x/badges and x/wasmx module.&#x20;

**x/badges**

* [MsgUpdateCollection](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/MsgUpdateCollection.html) - Creates a new collection or updates the details of a collection. Must be manager to execute.
* [MsgTransferBadges](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/MsgTransferBadges.html) - Transfer badges between users, if the rules allow.
* [MsgUpdateUserApprovedTransfers](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/MsgUpdateUserApprovedTransfers.html) - Set incoming / outgoing approvals for a collection, in addition to permissions which define the updatability of the approvals.
* [MsgDeleteCollection](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/MsgDeleteCollection.html) - Deletes the collection, if permissions allow. Must be manager.
* [MsgCreateAddressMappings](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/MsgCreateAddressMappings.html) - Creates address mapping(s).



For MsgUpdateCollection and MsgUpdateUserApprovedTransfers, we use an update flag + new value format. If the update flag is true, we will update it to the new value. If it is false, we do not update and ignore the value.

**x/wasmx**

* [MsgExecuteContractCompat](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/MsgExecuteContractCompat.html) - Helper Msg to support executing contracts from Ethereum wallets for EIP712. See [here](../tutorials/create-a-smart-contract.md) for tutorial.

**Other Cosmos SDK Modules**

For other standard Cosmos SDK messages, you can check out the bitbadgesjs-proto documentation (such as [MsgSend](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/MsgSend.html) here).



## Msg Definitions

```typescript
export interface MsgCreateAddressMappings {
  creator: string;
  addressMappings: AddressMapping[];
}
```

```typescript
export interface MsgDeleteCollection<T extends NumberType> {
  creator: string
  collectionId: T
}
```

```typescript
export interface MsgTransferBadges<T extends NumberType> {
  creator: string;
  collectionId: T;
  transfers: Transfer<T>[];
}
```

```typescript
export interface MsgUpdateCollection<T extends NumberType> {
  creator: string
  collectionId: T
  balancesType?: string
  defaultOutgoingApprovals?: UserOutgoingApproval<T>[]
  defaultIncomingApprovals?: UserIncomingApproval<T>[]
  defaultUserPermissions?: UserPermissions<T>
  badgesToCreate?: Balance<T>[]
  updateCollectionPermissions?: boolean
  collectionPermissions?: CollectionPermissions<T>
  updateManagerTimeline?: boolean
  managerTimeline?: ManagerTimeline<T>[]
  updateCollectionMetadataTimeline?: boolean
  collectionMetadataTimeline?: CollectionMetadataTimeline<T>[]
  updateBadgeMetadataTimeline?: boolean
  badgeMetadataTimeline?: BadgeMetadataTimeline<T>[]
  updateOffChainBalancesMetadataTimeline?: boolean
  offChainBalancesMetadataTimeline?: OffChainBalancesMetadataTimeline<T>[]
  updateCustomDataTimeline?: boolean
  customDataTimeline?: CustomDataTimeline<T>[]
  updateCollectionApprovals?: boolean
  collectionApprovals?: CollectionApproval<T>[]
  updateStandardsTimeline?: boolean
  standardsTimeline?: StandardsTimeline<T>[]
  updateContractAddressTimeline?: boolean
  contractAddressTimeline?: ContractAddressTimeline<T>[]
  updateIsArchivedTimeline?: boolean
  isArchivedTimeline?: IsArchivedTimeline<T>[]
}
```

```typescript
export interface MsgUpdateUserApprovals<T extends NumberType> {
  creator: string
  collectionId: T
  updateOutgoingApprovals?: boolean
  outgoingApprovals?: UserOutgoingApproval<T>[]
  updateIncomingApprovals?: boolean
  incomingApprovals?: UserIncomingApproval<T>[]
  updateUserPermissions?: boolean
  userPermissions?: UserPermissions<T>
}
```

# 🤝 Staking $BADGE

The BitBadges blockchain is a [delegated proof-of-stake blockchain](https://101blockchains.com/proof-of-stake-vs-delegated-proof-of-stake/). Delegated Proof of Stake (DPoS) is a blockchain consensus mechanism where token holders elect a small group of trusted members (validators) to validate transactions and maintain the network. These validators stake $BADGE and earn additional $BADGE from maintaining the network.&#x20;

It is delegated because token holders can delegate their stake ($BADGE) to trusted validators and split the earned $BADGE, according to their delegation and commission charged by the validator. This enabes token holders to contribute to the security of the network without running their own validator.

### **Become a Validator**

If you want to become a validator and stake directly by running a validating node, see [here](../blockchain/run-a-node.md).

### Delegate

For most users, delegating is the preferred option. You can delegate your $BADGE to be staked by an existing validator. This validator will run a validating node and split the $BADGE earned from securing the network with you (they may charge a commission).&#x20;



**How to Delegate?**

**Option 1 (Recommended):** Visit TODO. We are currently working on a simple user interface to delegate $BADGE to a validator.

**Option 2 (Technical):** If you are running a node, you can interact directly with the command line to delegate to a running validator.

**delegate**[**​**](https://docs.cosmos.network/main/modules/staking#delegate-1)

The command `delegate` allows users to delegate liquid tokens to a validator.

Usage:

```
bitbadgeschaind tx staking delegate [validator-addr] [amount] [flags]
```

Example:

```
simd tx staking delegate cosmosvaloper1l2rsakp388kuv9k8qzq6lrm9taddae7fpx59wm 1000badge --from mykey
```

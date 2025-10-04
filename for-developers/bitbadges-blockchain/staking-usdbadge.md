# Staking / Validators

The BitBadges blockchain is a [delegated proof-of-stake blockchain](https://101blockchains.com/proof-of-stake-vs-delegated-proof-of-stake/). Delegated Proof of Stake (DPoS) is a blockchain consensus mechanism where token holders elect a small group of trusted members (validators) to validate transactions and maintain the network. These validators stake their own stake and earn additional stake from maintaining the network.&#x20;

The delegated part is because token holders can delegate their stake to validators and split the earned, according to their delegation and commission charged by the validator. This enables token holders to contribute to the security of the network without running their own validator.

Reach out to us in Discord if you plan to run a validating node and need funds. To run a validator node, see [here](run-a-node/).&#x20;

BitBadges is also a delegated PoS chain, so you can delegate to an existing validator. This validator will split the amount earned from securing the network with you (they may charge a commission). Visit [https://bitbadges.io/stake](https://bitbadges.io/stake).&#x20;

Behind the scenes, this uses the [Cosmos SDK staking](https://docs.cosmos.network/main/modules/staking) module.&#x20;

## **Governance**

Initially, we do not plan to use a governance structure to allow us to ship fast and build a great prduct. Over time as we shift to becoming more decentralized, we will allow stakeholders to propose and vote on governance proposals, weighted via their amount. This will be via the [Cosmos SDK governance module](https://docs.cosmos.network/main/modules/gov). These governance proposals will allow holders to decide on the future of BitBadges.

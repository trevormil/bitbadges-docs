# Overview

In this guide, we will provide detailed instructions for setting up and running a BitBadges blockchain node. This should not be used as a universal guide, as there are many methods and best practices that you can use. However, we will cover each step and include examples to ensure a smooth setup.

The BitBadges blockchain is built using the Cosmos SDK, so if you have prior experience running a Cosmos SDK blockchain node, you will find this process quite familiar. If you encounter any issues during the setup process, you can also refer to other Cosmos SDK node documentation, such as "[Cosmos SDK - Running a Node](https://docs.cosmos.network/main/user/run-node/run-node)" or "[Cosmos Tutorials - Run in Production documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/1-overview.html)." These resources provide additional in-depth information and examples. Make sure that you replace everything with the corresponding BitBadges details where necessary.

**Becoming a Validator:** If you aspire to become a validator, reach out to us to receive a tokens for staking.

**Chain IDs:** When a chain ID is required, use the following:

-   "bitbadges-1" for the mainnet / betanet chain ID
-   "bitbadges-2" for the testnet chain ID

**Genesis JSON:** See [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain). Note different versions (testnets vs betanet vs mainnet) will have different genesis JSONs.

**BitBadges Public RPCs:** https://rpc.bitbadges.io

Node ID (betanet): 3958a0e660599d8146e7f2a6da8d4df83561b0fc

**State Snapshots:** See [Chain Details](../chain-details.md)

**Handling Upgrades:** BitBadges uses the x/upgrade module from Cosmos SDK for upgrades and expects upgrades to be handled with zero-downtime using Cosmovisor.

**Discord:** Communications and announcements for node operators is facilitated via our Discord.

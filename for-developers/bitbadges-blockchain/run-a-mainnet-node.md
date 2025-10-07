# Run a Mainnet Node

Running a node is the same as any other Cosmos SDK chain. If you encounter any issues during the setup process, you can also refer to other Cosmos SDK node documentation, such as "[Cosmos SDK - Running a Node](https://docs.cosmos.network/main/user/run-node/run-node)" or "[Cosmos Tutorials - Run in Production documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/1-overview.html)."&#x20;

Below are some links to get you started. If you are unfamiliar with setup, I'd recommend following a guide and/or asking for help in the #validators channel of our Discord. We have plenty of great validators maintaining the network, and many have detailed guides to help you get started. LLMs are also a good resource and can help you get set up.

Note that as a validator, you are responsible for the security of the network. Take necessary precautions for uptime and security in order to avoid slashing or losing staked funds.

**Discord**

Our main communication method is via Discord:

* \#validators channel is a great resource for help. Ping @trevormil to give you the Validator role.
* \#announcements and #chain-upgrades are where we announce the latest plans to upgrade. We use the x/upgrade module from Cosmos SDK. We recommend Cosmovisor for handling upgrades.

**Guides**

* [https://docs.provewithryd.xyz/mainnet/bitbadges/network-overview](https://docs.provewithryd.xyz/mainnet/bitbadges/network-overview)
* [https://nodestake.org/bitbadges](https://nodestake.org/bitbadges)

**Snapshots**

* [https://nodestake.org/bitbadges](https://nodestake.org/bitbadges)

**Releases / Instructions**

* [https://github.com/BitBadges/bitbadgeschain/releases](https://github.com/BitBadges/bitbadgeschain/releases)

**Genesis File**

* [https://github.com/BitBadges/bitbadgeschain/blob/master/genesis-711316.json](https://github.com/BitBadges/bitbadgeschain/blob/master/genesis-711316.json) (Note this is post 711316 hard fork)

**Explorer**

* [https://explorer.bitbadges.io/BitBadges%20Mainnet/staking](https://explorer.bitbadges.io/BitBadges%20Mainnet/staking)

**BitBadges RPC**

* https://rpc.bitbadges.io

**Running a Relayer?**

* See the official IBC connections in the Cosmos chain registry that we support.

**Configuration**

Most node parameters are flexible and you can set up according to your preferences. Here are some that should be the same as the rest of the network.&#x20;

* chain\_id: bitbadges-1
* timeout\_commit: 2s

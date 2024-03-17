# Run a Node

In this guide, we will provide detailed instructions for setting up and running a BitBadges blockchain node. This should not be used as a universal guide, as there are many methods and best practices that you can use. However, we will cover each step thoroughly and include examples to ensure a smooth setup.&#x20;

**Other Documentation:** The BitBadges blockchain is built using the Cosmos SDK, so if you have prior experience running a Cosmos SDK blockchain node, you will find this process quite familiar.  If you encounter any issues during the setup process, you can also refer to other Cosmos SDK node documentation, such as "[Cosmos SDK - Running a Node](https://docs.cosmos.network/main/user/run-node/run-node)" or "[Cosmos Tutorials - Run in Production documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/1-overview.html)." These resources provide additional in-depth information and examples. Make sure that you replace everything with the corresponding BitBadges details where necessary.

**Becoming a Betanet Validator:** If you aspire to become a betanet validator, reach out to us to receive a sufficient amount of $BADGE tokens for staking.

**Chain IDs:** When a chain ID is required, use the following:

* "bitbadges\_1-1" for the mainnet chain ID (currently inactive)
* "bitbadges\_1-2" for the betanet chain ID

**Genesis JSON:** See [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain). Note different versions (testnets vs betanet vs mainnet) will have different genesis JSONs.

**BitBadges Public RPC:** https://node.bitbadges.io/rpc (alias of http://node.bitbadges.io:26657)&#x20;

Tendermint Node ID (betanet): 859f19f7582fa74cf50a1dcf07f8386abf618596

**Handling Upgrades:** BitBadges uses the x/upgrade module from Cosmos SDK for upgrades and migrations which is compatible with Cosmovisor, a tool for zero downtime swapping Cosmos SDK binaries. We expect your node to be setup using Cosmovisor. We explain more below.

**Discord:** Communications and announcements for node operators is facilitated via our Discord.



**Step 0: Fetch And Build**

The source code lives at [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain). You can either 1) download the code and build from source, or 2) download the executable directly. For the latest releases, check the [releases page](https://github.com/BitBadges/bitbadgeschain/releases). The best practice is to do this as a non-root user.

If building from source, we refer you to the README of the repository.

The easier way is just downloading the executable directly. The executable can be found under the releases tab at [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain). Choose the correct executable for your machine and operating system.

Example: [https://github.com/BitBadges/bitbadgeschain/releases/tag/v1.0](https://github.com/BitBadges/bitbadgeschain/releases/tag/v1.0)

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

At the end of Step 0, you should have the bitbadgeschaind executable to run. Consider adding it to your PATH for easier execution. This may be **bitbadgeschaind** or something like **bitbadgeschain-linux-amd64.** We use bitbadgeschaind throughout this documentation. Adjust where necessary.

**Step 1: Initialization**

Initialize your chain by running

```shell
bitbadgeschaind init <moniker> --chain-id CHAIN_ID
```

You'll need to replace `<moniker>` with a custom username for your node and the CHAIN\_ID for the chain you want (bitbadges\_1-2 for betanet).

Take note of where your configuration files live. We call this DAEMON\_HOME throughout the rest of this documentation. This can be controlled with the --home flag if you do not want the default location. Run with --help to see the default.

**Step 2: Setup Node**&#x20;

Setting up your node infrastructure correctly and with best practices is crucial to ensure security. There are many options for this, so below we just give some general guidelines.&#x20;

* DDoS Mitigation: Being part of a network with a known IP address can expose you to DDoS attacks. Learn how to mitigate these risks [here](https://tutorials.cosmos.network/tutorials/9-path-to-prod/5-network.html#ddos). Consider using sentry nodes and proxies.
* Key Management: Implement best practices for key management, including key management systems such as [TMKMS](https://hub.cosmos.network/main/validators/kms/kms.html). This is especially important for validators.
* Redundancy: Plan for infrastructure failures such as power outages to ensure the continuous operation of your validator. Consider setting up your software as a service to avoid relaunching it manually every time. Refer to the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html#as-a-service) for guidance on configuring your node as a service.

**Setup - Validator Keys**

Follow the instructions in the [Cosmos documentation](https://docs.cosmos.network/main/run-node/keyring) and/or the [Cosmos Tutorials](https://tutorials.cosmos.network/tutorials/9-path-to-prod/3-keys.html) to set up your validator keys. Replace `simd` with `bitbadgeschaind` or `myproject` with `bitbadgeschain`.

A validator handles [two](https://hub.cosmos.network/main/validators/validator-faq.html#what-are-the-different-types-of-keys) perhaps three, different keys. Most likely keys 2 and 3 [are the same](https://github.com/cosmos/cosmos-sdk/blob/v0.46.1/proto/cosmos/staking/v1beta1/tx.proto#L45-L47). Each has a different purpose:

1. The **Tendermint consensus key** is used to sign blocks on an ongoing basis. It is of the key type `ed25519`, which the KMS can keep. When Bech-encoded, the address is prefixed with `cosmosvalcons` and the public key is prefixed with `cosmosvalconspub`.
2. The **validator operator application key** is used to create transactions that create or modify validator parameters. It is of type `secp256k1`, or whichever type the application supports. When Bech-encoded, the address is prefixed with `cosmosvaloper`.
3. The [**delegator application key** ](https://hub.cosmos.network/main/validators/validator-faq.html#are-validators-required-to-self-delegate-atom)is used to handle the stake that gives the validator more weight. When Bech-encoded, the address is prefixed with `cosmos` and the public key is prefixed with `cosmospub`.



**Consensus Key**

The consensus key is HOT, meaning it is needed on the validator node to sign blocks. It is strongly recommended that this is set up with a signing service and key management system.

By default, when you ran the **init** command, it creates a key for you in `..../config/priv_validator_key.json.`

This is convenient if you are starting a testnet, for which the security requirements are low. However, for a more valuable network, you should not store it directly in the node's filesystem. It should be stored in a more secure manner, such as with a signing service or key management system.&#x20;

**Operator / Delegator Key**

The application / operator key should be COLD and NOT be stored on the validator node. This is just your standard public/private key pair used to sign transactions.

This can be generated (if you don't already have one) by running the following command.

```
bitbadgeschaind keys --keyring-backend file --keyring-dir /root/.bitbadgeschain/keys
add <name>
```

Adjust the command accordingly. **Note you should run this and store it on your local desktop, not the validating node.**&#x20;



**Step 3: Configuring Your Node**&#x20;

IMPORTANT: You need to replace `DAEMON_HOME/genesis.json` with the agreed-upon version from [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain).&#x20;

Inside the config folder from Step 1's initialization, you'll find two files: `config.toml` and `app.toml`. Both files contain extensive comments to help you customize your node settings. You can also run `bitbadgeschaind start --help` for explanations. Tweak these as desired. Some important ones are highlighted below.

To establish connections with trusted nodes, edit `config.toml`. For instance:

```toml
persistent_peers = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656,
5735836cbaa747e013e47b11839db2c2990b918a@121.37.49.12:26656"
```

You may use 859f19f7582fa74cf50a1dcf07f8386abf618596@node.bitbadges.io:26656 for our node ID.

See betanet.genesis.json in source code repository for genesis file.These entries follow the format `nodeId@listenaddress:port`. Additionally, you can set up seed nodes by specifying them in the `seeds` field. Or, private peers at `private_peer_ids.`

Ensure that the listen address settings are correct, using your IP address or domain name if configured.  Also, make sure that your firewall exposes the necessary ports (22, 1317, 9090, 26656, 26657, 26660). See here for more information and other best practices running a node in production: [https://docs.cosmos.network/main/user/run-node/run-production#go](https://docs.cosmos.network/main/user/run-node/run-production#go).

**Step 4: Setting Up Cosmovisor**

BitBadges expects those running nodes to do so with Cosmovisor for zero-downtime upgrades, a tool for automating software upgrades in Cosmos SDK-based blockchains. You can find detailed information in the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html) and the [Cosmovisor documentation](https://docs.cosmos.network/main/tooling/cosmovisor.html). We use Cosmos SDK v0.47.5, so please use a compatible Cosmovisor version.

The first step is to download Cosmovisor as an executable using their documentation or building from source.

You will then need to set the following environment variables. The DAEMON\_HOME should be the home of your config files from Step 1. &#x20;

<pre class="language-docker"><code class="lang-docker"><strong>ENV DAEMON_HOME=/root/.bitbadgeschain
</strong>ENV DAEMON_NAME=bitbadgeschaind
</code></pre>

Then, run the following to setup your Cosmovisor directory.

```
cosmosvisor init <path_to_bitbadgeschaind_executable>
```

This will create the necessary folders and copy the executable into the DAEMON\_HOME/cosmovisor/genesis/bin.&#x20;

**Step 4: Starting the Blockchain** Before starting the blockchain, ensure that any additional services you configured (such as a key management service) is running. Make sure your computer or local network allows other nodes to initiate connections on port 26656. Ensure that firewall settings permit access to other required ports: 1317 (if hosting an API), 26660, 26657, and 9090.

To start your blockchain, simply run:

```shell
cosmovisor run start
```

This will run the blockchain with the configured settings and the genesis Cosmovisor executable. You can also pass them in as flags, but if you tweaked the config files as desired, you will be good.

Consider also running your node + Cosmovisor as a service, so you don't have to manually relaunch everytime. See [https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html](https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html) and [https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html](https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html)

**Step 4.5: BIP-322 Setup**

This is a temporary workaround to check Bitcoin signatures. Currently, there are no Go or Rust implementations of BIP-322 (the verification algorithm). As soon as there is, we will convert to a native implementation.

In the meantime, you will have to do the following.

Clone [https://github.com/BitBadges/bip322-js](https://github.com/BitBadges/bip322-js) in your DAEMON\_HOME directory. You should now have a bip322-js folder on the same level as cosmovisor, config, data, etc.

Navigate into the folder and run

```
npm i
npm run build
```

You should also make sure you have the node CLI command. The chain calls the following bash command to check signatures.

```
node DAEMON_HOME/dist/verify.js args...
```

Try a test run and make sure there are no errors. It should print true. Adjust for your home path.

```
node ./dist/verify.js '{\"account_number\":\"12\",\"chain_id\":\"bitbadges_1-2\",\"fee\":{\"amount\":[{\"amount\":\"0\",\"denom\":\"badge\"}],\"gas\":\"84362\"},\"memo\":\"\",\"msg0\":{\"type\":\"cosmos-sdk/MsgSend\",\"value\":{\"amount\":[{\"amount\":\"1\",\"denom\":\"badge\"}],\"from_address\":\"cosmos14t3uvy3qam7td3yufpp566wjp2q6nqtrf0qpyy\",\"to_address\":\"cosmos1uy4my3dwzwv9drgq06pt433z742l9vrlnx88ds\"}},\"sequence\":\"0\"}' 0247304402205a42d1b5973ce074818119ca7d61a1af2921f60a50aeff9eb7174aa1fb0686bd02205d2ae8896d3f67c9c74f14179bf49c697673dbdb2f851347a3cdc22cceaa13bc012102e600fba403d9a923446db973522e52e4ed5374f1bfe5448dfbab41f550b353f9 cosmos14t3uvy3qam7td3yufpp566wjp2q6nqtrf0qpyy
```

**Step 5: Joining the Validator Set** If you intend to run a validator node, execute the following command adjusted accordingly to join the set of validators (assuming you're not part of the initial genesis set). Run with --help for more details.

```shell
bitbadgeschaind tx staking create-validator /path/to/validator.json \
  --chain-id="name_of_chain_id" \
  --gas="auto" \
  --gas-adjustment="1.2" \
  --gas-prices="0.025badge" \
  --from=mykey
```

This should be signed with your normal key pair for signing transactions. Ensure you have enough $BADGE tokens to cover gas and your stake. The `validator.json` file should contain relevant information about your validator, including the consensus public key, moniker, website, security contact, details, commission rates, and min-self-delegation.

You can obtain the public validator consensus public key using the`bitbadgeschaind tendermint show-validator` command.

**Step 6: Handling Upgrades**

Any future executables will need to be downloaded from source again, in the same manner as Step 1. The executable will then need to be moved to the corresponding DAEMON\_HOME/cosmovisor/upgrades/\<upgrade-name>/bin folder. \<upgrade-name> is the name used in the x/upgrade module when proposing a new software upgrade.

Then once the new upgrade goes live (certain block height is reached), the executable will auto swap with zero downtime enabling the chain to continue.

The upgrades will be announced beforehand. You, as the node operator, will have until the upgrade time to successfully execute this step. If not completed by upgrade time, your node will be left behind and slashed.

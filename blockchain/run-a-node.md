# Run a Node

In this guide, we will provide detailed instructions for setting up and running a BitBadges blockchain node. The BitBadges blockchain is built using the Cosmos SDK, so if you have prior experience running a Cosmos SDK blockchain node, you will find this process quite familiar. However, we will cover each step thoroughly and include examples to ensure a smooth setup.

**Documentation Reference:** If you encounter any issues during the setup process, you can also refer to other Cosmos SDK node documentation, such as "[Cosmos SDK - Running a Node](https://docs.cosmos.network/main/user/run-node/run-node)" or "[Cosmos Tutorials - Run in Production documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/1-overview.html)." These resources provide additional in-depth information and examples. Make sure that you replace everything with the corresponding BitBadges details where necessary.

**Becoming a Betanet Validator:** If you aspire to become a betanet validator, reach out to us to receive a sufficient amount of $BADGE tokens for staking.

**Chain IDs:** When a chain ID is required, use the following:

* "bitbadges\_1-1" for the mainnet chain ID (currently inactive)
* "bitbadges\_1-2" for the betanet chain ID

**Genesis JSON:** TODO

**BitBadges Public RPC:** TODO

**Handling Upgrades:** BitBadges uses the x/upgrade module from Cosmos SDK for upgrades and migrations which is compatible with Cosmovisor. It is strongly recommended that you set up your node with Cosmovisor to handle and facilitate these upgrades in real time. We refer you to the  [https://docs.cosmos.network/v0.45/run-node/cosmovisor.html](https://docs.cosmos.network/v0.45/run-node/cosmovisor.html). See the reference Dockerfile at the end of this page for another reference.

**Discord:** Communications and announcements for node operators is facilitated via our Discord.





**Step 0: Fetch Source Code:** Begin by cloning the source code from the BitBadges GitHub repository:

```shell
git clone --branch {VERSION} https://github.com/bitbadges/bitbadgeschain
```

For the latest releases, check the [releases page](https://github.com/BitBadges/bitbadgeschain/releases). Replace VERSION with the desired release.

**Step 1: Building Binary** To build the BitBadges blockchain binary, you have two options:

_Option 1: Using Ignite CLI (Recommended)_ Install Ignite CLI and execute the following commands in the root directory of the cloned repository:

```shell
curl https://get.ignite.com/cli@v0.26.1 | bash
mv ignite /usr/local/bin # move to your path
ignite version # test that it is installed correctly
ignite chain build
ignite chain init --skip-proto
```

_Option 2: Using Makefile_ Ensure you have **make** and **go** installed on your machine, or use a Docker container that has them installed. Then, build the binary with:

```shell
make build-with-checksum
```

After building the binary, initialize your chain with:

```shell
bitbadgeschaind init <moniker> --chain-id CHAIN_ID --skip-proto
```

You'll need to replace `<moniker>` with a custom username for your node.

Take note of where your configuration files live. Default is typically `/root/.bitbadgeschain`.

**Step 2: Setup Node**&#x20;

Setting up your node infrastructure correctly and with best practices is crucial to ensure security. There are many options for this, so below we just give some general guidelines.&#x20;

* DDoS Mitigation: Being part of a network with a known IP address can expose you to DDoS attacks. Learn how to mitigate these risks [here](https://tutorials.cosmos.network/tutorials/9-path-to-prod/5-network.html#ddos). Consider using sentry nodes and proxies.
* Key Management: Implement best practices for key management, including key management systems such as [TMKMS](https://hub.cosmos.network/main/validators/kms/kms.html).
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



**Step 3: Configuring Your Node** Inside the config folder, you'll find two files: `config.toml` and `app.toml`. Both files contain extensive comments to help you customize your node settings. You can also run `bitbadgeschaind start --help` for explanations. Tweak as desired.

You should also replace `config/genesis.json` the agreed-upon version from above.

Ensure that the listen address settings are correct, using your IP address or domain name if configured.&#x20;

To establish connections with trusted nodes, edit `config.toml`. For instance:

```toml
persistent_peers = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656,
5735836cbaa747e013e47b11839db2c2990b918a@121.37.49.12:26656"
```

These entries follow the format `nodeId@listenaddress:port`. Additionally, you can set up seed nodes by specifying them in the `seeds` field. Or, private peers at `private_peer_ids.`

**Step 4: Starting the Blockchain** Before starting the blockchain, ensure that any additional services you configured (such as a key management service) is running. Make sure your computer or local network allows other nodes to initiate connections on port 26656. Ensure that firewall settings permit access to other required ports: 1317 (if hosting an c API), 26660, 26657, and 9090.

To start your blockchain, simply run:

```shell
bitbadgeschaind start
```

This will run the blockchain with the configured settings.&#x20;

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

**Step 6: Handling Upgrades** Over time, BitBadges will release software updates for the blockchain. To handle these upgrades efficiently, it is strongly recommended to use Cosmovisor, a tool for automating software upgrades in Cosmos SDK-based blockchains. You can find detailed information in the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html) and the [Cosmovisor documentation](https://docs.cosmos.network/main/tooling/cosmovisor.html).

Reference

For an example, refer to the provided Dockerfile. However, note that it's not recommended to run the entire setup within a Docker container, as Cosmovisor requires periodic binary updates, which may not be ideal for containers. Use the Dockerfile as a reference only.&#x20;

The following binaries go into the /upgrades/\<upgrade-name>/bin folder. \<upgrade-name> is the name used in the x/upgrade module when proposing a new software upgrade.

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-docker/blob/master/blockchain/Dockerfile" %}

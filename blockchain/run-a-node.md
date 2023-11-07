# Run a Node

In this guide, we will provide detailed instructions for setting up and running a BitBadges blockchain node. The BitBadges blockchain is built using the Cosmos SDK, so if you have prior experience running a Cosmos SDK blockchain node, you will find this process quite familiar. However, we will cover each step thoroughly and include examples to ensure a smooth setup.

**Documentation Reference:** If you encounter any issues during the setup process, you can also refer to other Cosmos SDK node documentation, such as "[Cosmos SDK - Running a Node](https://docs.cosmos.network/main/user/run-node/run-node)" or "[Cosmos Tutorials - Run in Production documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/1-overview.html)." These resources provide additional in-depth information and examples. Make sure that you replace everything with the BitBadges details.

**Becoming a Betanet Validator:** If you aspire to become a betanet validator, reach out to us to receive a sufficient amount of $BADGE tokens for staking.

**Chain IDs:** When a chain ID is required, use the following:

* "bitbadges\_1-1" for the mainnet chain ID (currently inactive)
* "bitbadges\_1-2" for the betanet chain ID

**Genesis JSON:** TODO

**BitBadges Public RPC:** TODO

**Handling Upgrades:** BitBadges uses the x/upgrade module from Cosmos SDK for upgrades and migrations. It is strongly recommended that you use Cosmovisor [https://docs.cosmos.network/v0.45/run-node/cosmovisor.html](https://docs.cosmos.network/v0.45/run-node/cosmovisor.html) to handle and facilitate these upgrades in real time.





**Step 0: Fetch Source Code:** Begin by cloning the source code from the BitBadges GitHub repository:

```shell
git clone --branch {VERSION} https://github.com/bitbadges/bitbadgeschain
```

For the latest releases, check the [releases page](https://github.com/BitBadges/bitbadgeschain/releases). Replace VERSION with the desired release.

**Step 1: Building Binary** To build the BitBadges blockchain binary, you have two options:

_Option 1: Using Ignite CLI (Recommended)_ Install Ignite CLI and execute the following commands in the root directory of the cloned repository:

```shell
curl https://get.ignite.com/cli@v0.26.1 | bash
mv ignite /usr/local/bin
ignite version # test installed correctly
ignite chain build
ignite chain init --skip-proto
```

_Option 2: Using Makefile_ Ensure you have **make** and **go** installed on your machine. Then, build the binary with:

```shell
make build-with-checksum
```

After building the binary, initialize your chain with:

```shell
bitbadgeschaind init <moniker> --chain-id CHAIN_ID --skip-proto
```

You'll need to replace `<moniker>` with a custom username for your node.

Next, replace `config/genesis.json` the agreed-upon version from above.

**Step 2: Setting Up Validator (If Applicable)** This step is necessary if you plan to run a validating node. Follow the instructions in the [Cosmos documentation](https://docs.cosmos.network/main/run-node/keyring) and/or the [Cosmos Tutorials](https://tutorials.cosmos.network/tutorials/9-path-to-prod/3-keys.html) to set up your validator and its keys. Replace `simd` with `bitbadgeschaind` or `myproject` with `bitbadgeschain`.

It's crucial to execute this step correctly to ensure the security of your validator. Consider the following factors:

* DDoS Mitigation: Being part of a network with a known IP address can expose you to DDoS attacks. Learn how to mitigate these risks [here](https://tutorials.cosmos.network/tutorials/9-path-to-prod/5-network.html#ddos).
* Key Management: Implement best practices for key management, including hardware key management systems.
* Redundancy: Plan for downtime and failures to ensure the continuous operation of your validator.

**Step 3: Configuring Your Node and Connecting to Others** Inside the config folder, you'll find two files: `config.toml` and `app.toml`. Both files contain extensive comments to help you customize your node settings. You can also run `bitbadgeschaind start --help` for explanations.

Ensure that the listen address settings are correct, using your IP address or domain name if configured. To establish connections with trusted nodes, edit `config.toml`. For instance:

```toml
persistent_peers = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656,
5735836cbaa747e013e47b11839db2c2990b918a@121.37.49.12:26656"
```

These entries follow the format `nodeId@listenaddress:port`. Additionally, you can set up seed nodes by specifying them in the `seeds` field.

**Step 5: Starting the Blockchain** Before starting the blockchain, ensure that any signing service (if set up in Step 2) is running. You can find helpful information in the [Cosmos documentation](https://docs.cosmos.network/main/run-node/run-production).

To start your blockchain, simply run:

```shell
bitbadgeschaind start
```

This will run the blockchain with the configured settings. Make sure your computer or local network allows other nodes to initiate connections on port 26656. Ensure that firewall settings permit access to other required ports: 1317, 26660, 26657, and 9090.

**Step 6: Running as a Service** For convenience, consider setting up your software as a service to avoid relaunching it manually every time. Refer to the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html#as-a-service) for guidance on configuring your node as a service.

**Step 7: Joining the Validator Set** If you intend to run a validator node, execute the following command adjusted accordingly to join the set of validators (assuming you're not part of the initial genesis set):

```shell
bitbadgeschaind tx staking create-validator /path/to/validator.json \
  --chain-id="name_of_chain_id" \
  --gas="auto" \
  --gas-adjustment="1.2" \
  --gas-prices="0.025badge" \
  --from=mykey
```

Ensure you have enough $BADGE tokens to cover gas and your stake. The `validator.json` file should contain relevant information about your validator, including the public key, moniker, website, security contact, details, commission rates, and min-self-delegation.

You can obtain the public key using the `bitbadgeschaind tendermint show-validator` command.

**Step 8: Handling Upgrades** Over time, BitBadges will release software updates for the blockchain. To handle these upgrades efficiently, it is strongly recommended to use Cosmovisor, a tool for automating software upgrades in Cosmos SDK-based blockchains. You can find detailed information in the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html) and the [Cosmovisor documentation](https://docs.cosmos.network/main/tooling/cosmovisor.html).

**References** For a complete example, refer to the provided Dockerfile. However, note that it's not recommended to run the entire setup within a Docker container, as Cosmovisor requires periodic binary updates, which may not be ideal for containers. Use the Dockerfile as a reference only.

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-docker/blob/master/blockchain/Dockerfile" %}

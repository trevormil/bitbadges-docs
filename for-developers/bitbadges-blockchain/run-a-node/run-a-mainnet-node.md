# Run a Mainnet Node

## DAEMON\_HOME

Your DAEMON\_HOME is the folder that will contain everything about the blockchain state, configuration, genesis, etc. Ensure that this folder persists across upgrades and changes. This is especially important if you plan to run the node using a container approach (Docker, Kubernetes, etc).

## Fetch / Build Binaries

### Download

Download the executable directly from GitHub. For the latest releases, check the [releases page](https://github.com/BitBadges/bitbadgeschain/releases). Choose the correct executable for your machine and operating system.

```
wget https://github.com/BitBadges/bitbadgeschain/releases/download/v1.0-betanet/bitbadgeschain-linux-amd64
```

Example: [https://github.com/BitBadges/bitbadgeschain/releases/tag/v1.0-betanet](https://github.com/BitBadges/bitbadgeschain/releases/tag/v1.0-betanet)

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (7).png" alt=""><figcaption></figcaption></figure>

If this is your first time downloading, you will need to also download the Wasm VM runtime library as well. This is the libwasmvm.x86\_64.so file and should be placed into /usr/lib. If not, you will get "error while loading shared libraries: libwasmvm.x86\_64.so: cannot open shared object file: No such file or directory".

Example

<pre><code>wget https://github.com/BitBadges/bitbadgeschain/releases/download/v1.0-betanet/bitbadgeschain-linux-amd64
<strong>wget https://github.com/BitBadges/bitbadgeschain/releases/download/v1.0-betanet/libwasmvm.x86_64.so
</strong><strong>mv libwasmvm.x86_64.so /usr/lib/
</strong></code></pre>

### **Build from Source**

We refer you to the README of the blockchain code repository.

### **Docker**

Running with Docker may be the easiest option, but it also is not compatible with automatic upgrades through Cosmovisor (see section below). This means you will sacrifice availability (which is especially important for validators who are slashed when down).

```bash
docker pull bitbadges/bitbadgeschain:latest
```

## Handling Upgrades - Cosmovisor

BitBadges handles binary upgrades using Cosmovisor, a tool for automating upgrades with zero downtime. You can find detailed information in the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html) and the [Cosmovisor documentation](https://docs.cosmos.network/main/tooling/cosmovisor.html). It is expected all validators are using Cosmovisor.

Upgrades will be announced in the Discord and are facilitated with the x/upgrades module behind the scenes. You, as the node operator, will have until the upgrade time to successfully handle the upgrade. If not completed by upgrade time, your node will halt at the upgrade height. If your node is a validator, it will be slashed.

### **Installation / Setup**

{% content-ref url="cosmovisor.md" %}
[cosmovisor.md](cosmovisor.md)
{% endcontent-ref %}

## RUN\_COMMAND

Depending on your setup method, you may have different commands to run the binary. Throughout the rest of this documentation, we use RUN\_COMMAND to avoid repeating ourselves. Please replace your command wherever you see RUN\_COMMAND

**Cosmovisor**

```bash
# The "run" is important. Ex:
# cosmovisor run init -> Blockchain initialixation
# cosmosvisor init -> Cosmosvisor folder initialization
cosmovisor run ....
```

**Plain Executable**

```
./bitbadgeschaind ....
```

**Docker**

<pre class="language-bash"><code class="lang-bash"><strong># Replace DAEMON_HOME
</strong><strong>docker run -it \
</strong>    -p 26656:26656 \
    -p 26657:26657 \
    -p 26660:26660 \
    -p 6060:6060 \
    -p 9090:9090 \
    -p 1317:1317 \
<strong>    --mount type=bind,source="$DAEMON_HOME",target=/root/.bitbadgeschain \
</strong>    bitbadges/bitbadgeschain:latest ... 
</code></pre>

## Initialization / Syncing

In order to catch up to the current consensus, you will need to get your node synced. This can be done from genesis (time consuming but no trust needed) or from a recent snapshot of the state (much faster but requires slight trust assumptions).

### **From Snapshosts (Recommended)**

If you do not want to reconstruct the entire history of the chain from genesis, you can start from a checkpoint. This can potentially save you days of syncing but requires you to trust an existing node.

{% embed url="https://explorer.bitbadges.io/BitBadges%20Mainnet/statesync" %}

**State Sync**

You can configure your config.toml to use the Cosmos SDK state sync feature to quickly sync from a trusted node. Feel free to use the official RPC node to do this. We refer you to here [https://docs.tendermint.com/v0.34/tendermint-core/state-sync.html](https://docs.tendermint.com/v0.34/tendermint-core/state-sync.html) or you can reference other Cosmos SDK state sync documentation.

**From Snapshots**

You can get the necessary files from an existing snapshot, add them to your DAEMON\_HOME, and start the chain. See [Chain Details](../chain-details.md) for providers.

For [https://snapshots.whenmoonwhenlambo.money/bitbadges-1](https://snapshots.whenmoonwhenlambo.money/bitbadges-1), use this command. Replace /cosmos/.bitbadgeschain with your home directoyry.

```
lz4 -c -d bitbadges-1-snapshot-latest.tar.lz4 | tar -x -C /cosmos/.bitbadgeschain
```

### **From Block Sync**

Syncing from genesis or via block sync means that you start with the blank genesis state and verify all transactions from the start block to the current block (time consuming). This is not recommended unless you need to run a full archive node.

**Chain Binaries**

The chain binary may be upgraded over time. To continue syncing, you will always need the relevant binary for the current block. This means you must handle ALL chain upgrades (since you are syncing from genesis). See Cosmovisor section.

**Chain Forks**

```
NOTE: We had a hard fork after block 711315
```

Normal Comet BFT block sync will not work from block 711315 -> 711316 due to this fork. You will need to manually handle this.

For handling this, you can either:

1. Start with block 711316 genesis or later (recommended). Everything will work as intended.

<pre><code>cd DAEMON_HOME/config
<strong>rm genesis.json
</strong>curl -o genesis.json https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/genesis-711316.json
RUN_COMMAND comet unsafe-reset-all
RUN_COMMAND start
</code></pre>

2. Or if you really need blocks 1-711315 for a full archive node (maybe like an explorer). Please reach out if you are planning to use this approach as we can help you through this process.

See ./scripts/handle-fork-711315.sh in the btibadgeschain GitHub repository for a full script. Or, do the following below:

You can get blocks 1-711315 via running it below or via a snapshot (recommended).

```
cd DAEMON_HOME/config
rm genesis.json
curl -o genesis.json https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/genesis.json
RUN_COMMAND start 
```

If you want to migrate and join together the blockstores / transaction indexes so they are all in one place:

<pre class="language-bash"><code class="lang-bash"># Backup the /data folder for blocks 1-711315 somewhere for later use
# Get the chain up and running for 711316+ (see above). Sync a few blocks. Stop the chain. 

<strong>git clone https://github.com/bitbadges/bitbadgeschain
</strong><strong># Edit migrate.go to use your intended source / target DB paths
</strong><strong># Run migrate.go which copies all blockstores / transaction data and sets base height back to 1
</strong><strong># Note: If you need more than just block data (cs.wal, state.db, or evidence.dd), this is left  up to you. 
</strong>go run scripts/migrate.go -source /path/to/snapshot/data -target /path/to/target/data
</code></pre>

**Testnet Genesis**

Note: Replace the genesis files with the corresponding testnet ones if you are planning to run a testnet node

## Configuration

To initialize a new chain, run the following (depending on your build method). CHAIN\_ID will be "bitbadges-1" for mainnet. Initialization should only be performed once.

```
RUN_COMMAND init <moniker> --chain-id CHAIN_ID
```

You'll need to replace `<moniker>` with a custom username for your node and the CHAIN\_ID for the chain you want (bitbadges-1 for mainnet).

Take note of where your configuration files live. We expect it to be in /root/.bitbadgeschain but if it isn't, you will need to make sure it is correct with --home flags. We call this DAEMON\_HOME.

If you are getting directories do not exist error, you may have to do the following first. These will be overwritten when the init command is executed, but it is just to get the errors out of there.

<pre><code><strong>cd DAEMON_HOME
</strong>mkdir config
mkdir data
mkdir wasm
</code></pre>

Inside the DAEMON\_HOME/config folder, you'll find two files: `config.toml` and `app.toml`. Both files contain extensive comments to help you customize your node settings. You can also run `RUN_COMMAND start --help` for explanations.

Tweak these as desired. Some important ones are highlighted below.

**Setting Seed Nodes**

In order to pull data from other nodes, you need to have seed nodes or peers to pull from. You may use 2703c1304a70186372aa726a762d60da94c29ffe@node.bitbadges.io:26656 for our official node ID. To establish connections with trusted nodes, edit `config.toml`. For instance:

```toml
seeds = "2703c1304a70186372aa726a762d60da94c29ffe@node.bitbadges.io:26656"
```

These entries follow the format `nodeId@listenaddress:port`. Additionally, you can set up seed nodes by specifying them in the `seeds` field. Or, private peers at `private_peer_ids.`

**Listen Addresses / Firewalls**

Ensure that the listen address settings are correct, using your IP address or domain name if configured. Also, make sure that your firewall exposes the necessary ports (22, 1317, 9090, 26656, 26657, 26660). See here for more information and other best practices running a node in production: [https://docs.cosmos.network/main/user/run-node/run-production#go](https://docs.cosmos.network/main/user/run-node/run-production#go).

## Running the Node

Once you have the node all configured, run the following to start the chain. You should see blocks being synced if configured correctly.

```
RUN_COMMAND start
```

**Other Considerations**

Setting up your node infrastructure correctly and with best practices is crucial to ensure security. There are many options for this, so below we just give some general guidelines.

* DDoS Mitigation: Being part of a network with a known IP address can expose you to DDoS attacks. Learn how to mitigate these risks [here](https://tutorials.cosmos.network/tutorials/9-path-to-prod/5-network.html#ddos). Consider using sentry nodes and proxies.
* Key Management: Implement best practices for key management, including key management systems such as [TMKMS](https://hub.cosmos.network/main/validators/kms/kms.html). This is especially important for validators.
* Redundancy: Plan for infrastructure failures such as power outages to ensure the continuous operation of your validator. Consider setting up your software as a service to avoid relaunching it manually every time. Refer to the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html#as-a-service) for guidance on configuring your node as a service.
* Consider also running your node + Cosmovisor as a service, so it relaunches automatically. See [https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html](https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html) and [https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html](https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html)

## Running a Validator

As explained in the [Launch Phases](../../../overview/launch-phases.md) docs, reach out to us in Discord if you plan to run a validating node and need $STAKE. This is free and only requires a quick application process.

**Setup - Validator Keys**

Follow the instructions in the [Cosmos documentation](https://docs.cosmos.network/main/run-node/keyring) and/or the [Cosmos Tutorials](https://tutorials.cosmos.network/tutorials/9-path-to-prod/3-keys.html) to set up your validator keys. Replace `simd` with `bitbadgeschaind` or `myproject` with `bitbadgeschain`.

A validator handles [two](https://hub.cosmos.network/main/validators/validator-faq.html#what-are-the-different-types-of-keys) perhaps three, different keys. Most likely keys 2 and 3 [are the same](https://github.com/cosmos/cosmos-sdk/blob/v0.46.1/proto/cosmos/staking/v1beta1/tx.proto#L45-L47). Each has a different purpose:

1. The **Tendermint consensus key** is used to sign blocks on an ongoing basis. It is of the key type `ed25519`, which the KMS can keep. When Bech-encoded, the address is prefixed with `bbvalcons` and the public key is prefixed with `bbvalconspub`.
2. The **validator operator application key** is used to create transactions that create or modify validator parameters. It is of type `secp256k1`, or whichever type the application supports. When Bech-encoded, the address is prefixed with `bbvaloper`.
3. The [**delegator application key** ](https://hub.cosmos.network/main/validators/validator-faq.html#are-validators-required-to-self-delegate-atom)is used to handle the stake that gives the validator more weight. When Bech-encoded, the address is prefixed with `bb` and the public key is prefixed with `bbpub`.

**Consensus Key**

The consensus key is HOT, meaning it is needed on the validator node to sign blocks. It is strongly recommended that this is set up with a signing service and key management system.

By default, when you ran the **init** command, it creates a key for you in `..../config/priv_validator_key.json.`

This is convenient if you are starting a testnet, for which the security requirements are low. However, for a more valuable network, you should not store it directly in the node's filesystem. It should be stored in a more secure manner, such as with a signing service or key management system.

**Operator / Delegator Key**

The application / operator key should be COLD and NOT be stored on the validator node. This is just your standard public/private key pair used to sign transactions.

This can be generated (if you don't already have one) by running the following command.

```
bitbadgeschaind keys --keyring-backend file --keyring-dir /root/.bitbadgeschain/keys
add <name>
```

Adjust the command accordingly. **Note you should run this and store it on your local desktop, not the validating node.**

### Joining the Validator Set

If you intend to run a validator node, execute the following command adjusted accordingly to join the set of validators (assuming you're not part of the initial genesis set). Run with --help for more details. Replace bitbadgeschaind tx with your run command (dependent on your build method). This is the same command as how you started the node (but replace start with tx).

```shell
RUN_COMMAND tx staking create-validator /path/to/validator.json \
  --chain-id="name_of_chain_id" \
  --gas="auto" \
  --gas-adjustment="1.2" \
  --gas-prices="0.025ubadge" \
  --from=mykey
```

This should be signed with your normal key pair for signing transactions. Ensure you have enough $BADGE credits to cover gas and your stake. The `validator.json` file should contain relevant information about your validator, including the consensus public key, moniker, website, security contact, details, commission rates, and min-self-delegation.

You can obtain the public validator consensus public key using the`bitbadgeschaind tendermint show-validator` command.

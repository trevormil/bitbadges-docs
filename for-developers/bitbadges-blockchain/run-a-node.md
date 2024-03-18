# Run a Node

In this guide, we will provide detailed instructions for setting up and running a BitBadges blockchain node. This should not be used as a universal guide, as there are many methods and best practices that you can use. However, we will cover each step thoroughly and include examples to ensure a smooth setup.&#x20;

**Other Documentation:** The BitBadges blockchain is built using the Cosmos SDK, so if you have prior experience running a Cosmos SDK blockchain node, you will find this process quite familiar.  If you encounter any issues during the setup process, you can also refer to other Cosmos SDK node documentation, such as "[Cosmos SDK - Running a Node](https://docs.cosmos.network/main/user/run-node/run-node)" or "[Cosmos Tutorials - Run in Production documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/1-overview.html)." These resources provide additional in-depth information and examples. Make sure that you replace everything with the corresponding BitBadges details where necessary.

**Becoming a Betanet Validator:** If you aspire to become a betanet validator, reach out to us to receive a sufficient amount of $BADGE tokens for staking.

**Chain IDs:** When a chain ID is required, use the following:

* "bitbadges\_1-1" for the mainnet chain ID (currently inactive)
* "bitbadges\_1-2" for the betanet chain ID

**Genesis JSON:** See [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain). Note different versions (testnets vs betanet vs mainnet) will have different genesis JSONs.

**BitBadges Public RPCs:** https://node.bitbadges.io/rpc (alias of http://node.bitbadges.io:26657)&#x20;

Node ID (betanet): 3958a0e660599d8146e7f2a6da8d4df83561b0fc

**Handling Upgrades:** BitBadges uses the x/upgrade module from Cosmos SDK for upgrades and migrations which is compatible with Cosmovisor, a tool for zero downtime swapping Cosmos SDK binaries. We expect your node to be setup using Cosmovisor. We explain more below.

**Discord:** Communications and announcements for node operators is facilitated via our Discord.



## Fetch / Build Binaries

You have a couple options for fetching / building binaries. The source code lives at [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain).&#x20;

### **Docker**

```
git clone https://github.com/BitBadges/bitbadges-docker
cd bitbadges-docker
docker build -t bitbadgeschaind .
```

### **Build from Source**

If building from source, we refer you to the README of the repository.

### **Download Directly**

Download the executable directly. For the latest releases, check the [releases page](https://github.com/BitBadges/bitbadgeschain/releases). Choose the correct executable for your machine and operating system.&#x20;

Example: [https://github.com/BitBadges/bitbadgeschain/releases/tag/v1.0](https://github.com/BitBadges/bitbadgeschain/releases/tag/v1.0)

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Handling Upgrades - Cosmovisor

BitBadges handles binary upgrades using Cosmovisor, a tool for automating software upgrades in Cosmos SDK-based blockchains with zero downtime enabling the chain to continue operating. You can find detailed information in the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html) and the [Cosmovisor documentation](https://docs.cosmos.network/main/tooling/cosmovisor.html). We use Cosmos SDK v0.47.5, so please use a compatible Cosmovisor version. It is expected all validators are using Cosmovisor.

Upgrades will be announced in the Discord and are facilitated with the x/upgrades module behind the scenes. You, as the node operator, will have until the upgrade time to successfully handle the upgrade. If not completed by upgrade time, your node will halt at the upgrade height. If your node is a validator, it will be slashed.

If you are syncing from genesis, you will need all executables to be able to sync to the current state. If you are syncing from a later time, you will only need the binaries used after that time.

### **Docker**

**Setting Up**

No additional setup is required.

**New Upgrades**

If you are running with Docker, all you need to do is simply pull the latest build and restart. All binary upgrades are handled for you within the build.

<pre class="language-bash"><code class="lang-bash"><strong># Stop executable
</strong>git pull # from the bitbadges-docker repo
docker build -t bitbadgeschaind .
# Restart executable
</code></pre>

### **Manual**

**Setting Up Cosmovisor**

The first step is to download Cosmovisor as an executable using their documentation. You will then need to set the following environment variables. The DAEMON\_HOME should be the home of your config files. &#x20;

```docker
DAEMON_HOME=/root/.bitbadgeschain
DAEMON_NAME=bitbadgeschaind
```

Then, run the following to setup your Cosmovisor directory. The executable should be named bitbadgeschaind (if not, please rename).

```
cosmosvisor init ./bitbadgeschaind
```

This will create the necessary folders and copy the executable into the DAEMON\_HOME/cosmovisor/genesis/bin.&#x20;

**Adding Upgrades**

For a given upgrade, it will have a new binary and a \<upgrade-name>.  \<upgrade-name> is the name used in the x/upgrade module when proposing a new software upgrade.&#x20;

Depending on your version of cosmovisor, you may be able to run the following. Again, make sure the binary name is bitbadgeschaind.

```
cosmovisor add-upgrade ... 
```

Or, to manually upgrade, do the following.

1. Download the new binary and name it bitbadgeschaind. Do this in a separate folder to not interfere with anything currently running.
2. Create the DAEMON\_HOME/cosmovisor/upgrades/\<upgrade-name> and DAEMON\_HOME/cosmovisor/upgrades/\<upgrade-name>/bin directory.
3. Copy the new upgrade executable to the folder (keeping its name as bitbadgeschaind).

## Initialization

To initialize a new chain, run the following (depending on your build method). CHAIN\_ID will be "bitbadges\_1-2" for betanet. Initialization should only be performed once.

```
cosmovisor run init <moniker> --chain-id CHAIN_ID
```

<pre class="language-bash"><code class="lang-bash"># Replace DAEMON_HOME, &#x3C;moniker>, and CHAIN_ID
docker run -it \
    -p 26656:26656 \
    -p 26657:26657 \
    -p 26660:26660 \
    -p 6060:6060 \
    -p 9090:9090 \
    -p 1317:1317 \
<strong>    --mount type=bind,source="$DAEMON_HOME",target=/root/.bitbadgeschain \
</strong>    --mount type=volume,dst=/root/.bitbadgeschain/cosmovisor \
    --mount type=volume,dst=/root/.bitbadgeschain/bip322-js \
    bitbadgeschaind run init &#x3C;moniker> --chain-id CHAIN_ID
</code></pre>

You'll need to replace `<moniker>` with a custom username for your node and the CHAIN\_ID for the chain you want (bitbadges\_1-2 for betanet).

Take note of where your configuration files live. We expect it to be in /root/.bitbadgeschain but if it isn't, you will need to make sure it is correct with --home flags. We call this DAEMON\_HOME.&#x20;

If you are getting directories do not exist error, you may have to do the following first. These will be overwritten when the init command is executed, but it is just to get the errors out of there.

```
cd DAEMON_HOME
mkdir config
mkdir data
mkdir wasm
```

**Fetching the Correct Genesis JSON**

The init command creates a default genesis, but you will need to replace it with the agreed upon version.&#x20;

To do this for betanet, execute the following:

```
cd DAEMON_HOME/config
rm genesis.json
curl -o genesis.json https://raw.githubusercontent.com/BitBadges/bitbadgeschain/master/betanet.genesis.json
```

## Configuration

Inside the DAEMON\_HOME/config folder, you'll find two files: `config.toml` and `app.toml`. Both files contain extensive comments to help you customize your node settings. You can also run `bitbadgeschaind start --help` for explanations. Tweak these as desired. Some important ones are highlighted below.

**Setting Seed Nodes**

In order to pull data from other nodes, you need to have seed nodes or peers to pull from.

You may use 3958a0e660599d8146e7f2a6da8d4df83561b0fc@node.bitbadges.io:26656 for our node ID for betanet. To establish connections with trusted nodes, edit `config.toml`. For instance:

```toml
seeds = "3958a0e660599d8146e7f2a6da8d4df83561b0fc@node.bitbadges.io:26656"
```

These entries follow the format `nodeId@listenaddress:port`. Additionally, you can set up seed nodes by specifying them in the `seeds` field. Or, private peers at `private_peer_ids.`

**Listen Addresses / Firewalls**

Ensure that the listen address settings are correct, using your IP address or domain name if configured.  Also, make sure that your firewall exposes the necessary ports (22, 1317, 9090, 26656, 26657, 26660). See here for more information and other best practices running a node in production: [https://docs.cosmos.network/main/user/run-node/run-production#go](https://docs.cosmos.network/main/user/run-node/run-production#go).

## BIP322-JS

This is a temporary workaround to check Bitcoin signatures. Currently, there are no Go or Rust implementations of BIP-322 (the verification algorithm). As soon as there is, we will convert to a native implementation.

In the meantime, you will have to do the following.

**Docker**

This is handled for you.

**Manual**

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

## Running the Node

Once you have the node all configured, run

```
cosmovisor run start
```

```bash
#Replace DAEMON_HOME with yours
docker run -it \
    -p 26656:26656 \
    -p 26657:26657 \
    -p 26660:26660 \
    -p 6060:6060 \
    -p 9090:9090 \
    -p 1317:1317 \
    --mount type=bind,source="$DAEMON_HOME",target=/root/.bitbadgeschain \
    --mount type=volume,dst=/root/.bitbadgeschain/cosmovisor \
    --mount type=volume,dst=/root/.bitbadgeschain/bip322-js \
    bitbadgeschaind run start
```

**Other Considerations**

Setting up your node infrastructure correctly and with best practices is crucial to ensure security. There are many options for this, so below we just give some general guidelines.&#x20;

* DDoS Mitigation: Being part of a network with a known IP address can expose you to DDoS attacks. Learn how to mitigate these risks [here](https://tutorials.cosmos.network/tutorials/9-path-to-prod/5-network.html#ddos). Consider using sentry nodes and proxies.
* Key Management: Implement best practices for key management, including key management systems such as [TMKMS](https://hub.cosmos.network/main/validators/kms/kms.html). This is especially important for validators.
* Redundancy: Plan for infrastructure failures such as power outages to ensure the continuous operation of your validator. Consider setting up your software as a service to avoid relaunching it manually every time. Refer to the [Cosmos documentation](https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html#as-a-service) for guidance on configuring your node as a service.
* Consider also running your node + Cosmovisor as a service, so it relaunches automatically. See [https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html](https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html) and [https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html](https://tutorials.cosmos.network/tutorials/9-path-to-prod/7-migration.html)

## Running a Validator

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

### Joining the Validator Set

If you intend to run a validator node, execute the following command adjusted accordingly to join the set of validators (assuming you're not part of the initial genesis set). Run with --help for more details. Replace bitbadgeschaind tx with your run command (dependent on your build method). This is the same command as how you started the node (but replace start with tx).

```shell
cosmovisor run tx staking create-validator /path/to/validator.json \
  --chain-id="name_of_chain_id" \
  --gas="auto" \
  --gas-adjustment="1.2" \
  --gas-prices="0.025badge" \
  --from=mykey
```

```
docker run -it \
    -p 26656:26656 \
    -p 26657:26657 \
    -p 26660:26660 \
    -p 6060:6060 \
    -p 9090:9090 \
    -p 1317:1317 \
    --mount type=bind,source="$DAEMON_HOME",target=/root/.bitbadgeschain \
    --mount type=volume,dst=/root/.bitbadgeschain/cosmovisor \
    --mount type=volume,dst=/root/.bitbadgeschain/bip322-js \
    bitbadgeschaind run tx staking create-validator /path/to/validator.json \
    --chain-id="name_of_chain_id" \
    --gas="auto" \
    --gas-adjustment="1.2" \
    --gas-prices="0.025badge" \
    --from=mykey
```

This should be signed with your normal key pair for signing transactions. Ensure you have enough $BADGE tokens to cover gas and your stake. The `validator.json` file should contain relevant information about your validator, including the consensus public key, moniker, website, security contact, details, commission rates, and min-self-delegation.

You can obtain the public validator consensus public key using the`bitbadgeschaind tendermint show-validator` command.

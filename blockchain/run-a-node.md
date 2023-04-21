# Run a Node

Below, we will walk you through a tutorial for setting up and running a BitBadges blockchain node. The BitBadges blockchain is built with Cosmos SDK, so if you have previous experience running a Cosmos SDK blockchain node, it will be very similar.&#x20;

This also means documentation is similar, so if you are having problems with this tutorial, you can also visit Cosmos SDK node documentation, such as [Cosmos SDK - Running a Node](https://docs.cosmos.network/main/run-node/keyring) or [Cosmos Tutorials - Run in Production](https://tutorials.cosmos.network/tutorials/9-path-to-prod/) documentation.\


**Chain IDs**

Wherever a chain ID is required, the following should be used:

"bitbadges\_1-1" - betanet chain

"bitbadges\_1-2" - testnet chain



**Step 0: Fetch Source Code**

Clone the source code from [https://github.com/bitbadges/bitbadgeschain](https://github.com/bitbadges/bitbadgeschain).

**Step 1: Building Binary**

Option 1 (Recommended): Install [Ignite CLI](https://docs.ignite.com/) and run **ignite chain build** then **ignite chain init** in the root directory.

Option 2: Install via the Makefile. Note you must have **make** installed on your machine. On Ubuntu, use this command

```
sudo apt-get install --yes make
```

Then, build the binary with

```
make build-with-checksum
```

Then, initialize your chain with

```bash
# The argument <moniker> is the custom username of your node, it should be human-readable.
bitbadgeschaind init <moniker> --chain-id CHAIN_ID
```

Next, you will need to overwrite the genesis file with the agreed upon version. We have conveniently provided in the root directory: **./genesis.json**.

```bash
cp ./genesis.json ~/.bitbadgeschaind/config/genesis.json
```

**Step 2: Setup Validator Keys**&#x20;

This step is only required if you are running a validator.

Follow the steps in [https://docs.cosmos.network/main/run-node/keyring](https://docs.cosmos.network/main/run-node/keyring) and/or [https://tutorials.cosmos.network/tutorials/9-path-to-prod/3-keys.html](https://tutorials.cosmos.network/tutorials/9-path-to-prod/3-keys.html) to setup your validators' keys. Replace **simd** with **bitbadgeschaind** or **myproject** with **bitbadgeschain**.

Note performing this step correctly is critical to maintain the security of your validator.

**Step 3: Configuring Your Node and Connecting to Others**

Within **\~/.bitbadgeschaind/config**, there are two files: **config.toml** and **app.toml**. Both files are heavily commented, please refer to them directly to tweak the settings of your node. You can also run **bitbadgeschaind start --help** to get explanations.

Make sure the listen address settings are correct and use your IP address (e.g. 172.217.22.14) or domain name (if configured).

To establish persistent nodes that you trust, edit **config.toml.** For example,&#x20;

```toml
persistent_peers = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656,
5735836cbaa747e013e47b11839db2c2990b918a@121.37.49.12:26656"
```

These are in the format nodeId@listenaddress:port.

To establish seed node(s), set&#x20;

```toml
seeds = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656"
```

BitBadges Team Node: **TODO**@api.bitbadges.io:26656

**Step 4: Preventing DDoS**

Being part of a network with a known IP address can be a security or service risk. Distributed denial-of-service (DDoS) is a classic kind of network attack, but there are ways to mitigate the risks. See [https://tutorials.cosmos.network/tutorials/9-path-to-prod/5-network.html#ddos](https://tutorials.cosmos.network/tutorials/9-path-to-prod/5-network.html#ddos) for how.

**Step 5: Start Blockchain**

Make sure the signing service setup in Step 2 is started and running (this link may be helpful [https://docs.cosmos.network/main/run-node/run-production](https://docs.cosmos.network/main/run-node/run-production)).

Next, you can simply start your blockchain with

<pre><code><strong>bitbadgeschaind start
</strong></code></pre>

Make sure your computer or local network allows other nodes to initiate a connection to your node on port `26656`. It may be blocked via firewall(s). Other ports that should be open include 1317, 26660, 26657, and 9090.

**Step 6: Run as a service?**

Instead of relaunching your software every time, it is a good idea to set it up as a service.

See [**https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html#as-a-service**](https://tutorials.cosmos.network/tutorials/9-path-to-prod/6-run.html#as-a-service)&#x20;

**Step 7: Join Validator Set?**

If you want to run a validator and are not part of the initial genesis set of validators, you can execute the command below to join the set of validators ([https://docs.cosmos.network/main/modules/staking#create-validator](https://docs.cosmos.network/main/modules/staking#create-validator)). Note you must have enough $BADGE to pay for gas and your added stake.

<pre><code><strong>bitbadgeschaind tx staking create-validator [path/to/validator.json] [flags]
</strong></code></pre>

Example:

```
bitbadgeschaind tx staking create-validator /path/to/validator.json \
  --chain-id="name_of_chain_id" \
  --gas="auto" \
  --gas-adjustment="1.2" \
  --gas-prices="0.025badge" \
  --from=mykey
```

where `validator.json` contains:

```
{
  "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"BnbwFpeONLqvWqJb3qaUbL5aoIcW3fSuAp9nT3z5f20="},
  "amount": "1000000badge",
  "moniker": "my-moniker",
  "website": "https://myweb.site",
  "security": "security-contact@gmail.com",
  "details": "description of your validator",
  "commission-rate": "0.10",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.01",
  "min-self-delegation": "1"
}
```

and pubkey can be obtained by using `simd tendermint show-validator` command.

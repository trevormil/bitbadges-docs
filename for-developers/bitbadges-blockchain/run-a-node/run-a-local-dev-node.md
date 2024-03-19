# Run a Local Dev Node

Running a local development node follows pretty much the same instructions as the mainnet ones except the following.

* Chain ID should be something non-conflicting (i.e. not bitbadges\_1-1 or \_1-2)
* No need for seed nodes or peers (since you are running a single node local chain)
* Assuming you want to start a new chain with the latest binary, you will not have any upgrades to handle. Genesis -> Current Block will always be the current, latest binary.
  * If you are using Docker, you should be using the latest binary image instead of the mainnet node one. The mainnet node one has all binaries built.
* You can also start with an existing genesis / snapshot if you would like.

Please reach out in the dev Discord if you are stuck or need help!

**Funding Your Address**

With a local development chain started from scratch, you probably want to have an address that is seeded with some starting balances. You can fund your address by editing the DAEMON\_HOME/config/genesis.json -> app\_state.bank.balances path as seen below.

You can use any converted Cosmos address (e.g. an Ethereum address converted), but we recommend using an address you can sign with via the CLI.  To get one or view your existing ones, run&#x20;

```
cosmovisor run keys ...
```

```json
...
  "bank": {
      ...
      "balances": [
        {
          "address": "cosmos1qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqnrql8a",
          "coins": [
            {
              "denom": "badge",
              "amount": "1"
            }
          ]
        },
...
```

**Interacting with the Chain**

Cosmos SDK has an in-depth CLI for interacting with the chain and getting details about it. To see all commands or get help, simply run any command with --help (or an invalid command will auto print the help messages). &#x20;

The main ones you will be using are **tx** and **query** to sign transactions and query state, respectively**.**&#x20;

For example,

```bash
# --from keystorename is to sign the tx
cosmovisor run tx badges transfer-badges ... --from keystorename
```

```
cosmovisor run query badges get-balance ...
```

We refer you to the CLI docs or other Cosmos SDK documentation for more information. The best way to get started is to simply just try stuff in the CLI, in our opinion.

**Ports**

http://localhost:1317 will be the node's REST API port for queries. Navigate to it in a browser to see.

http://localhost:26657 will be the Tendermint RPC. You can also navigate here in the browser.

**Running the Full-Stack**

To run the indexer and frontend along with a local development chain, we refer you to the documentation for those. Make sure that the URLs are configured properly (i.e. pointing to localhost and not the main deployed one).&#x20;

Please reach out if you have problems.

# Broadcasting and Signing Txs

To learn more about broadcasting transactions with Cosmos SDK, you can visit [https://docs.cosmos.network/v0.46/run-node/txs.html](https://docs.cosmos.network/v0.46/run-node/txs.html).

For the transaction Msg types offered by BitBadges, see [Tx Msg Interfaces](../for-developers/concepts/msgs.md).

The recommended way to broadcast a transaction is by simply visiting [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast). This is a simple interface to just copy and paste your pregenerated Msgs into. The gas, transaction signers, signing the transaction is all outsourced to the interface. All you need to provide is copy and paste your Msg contents.

You can also use the [BitBadges SDK](broken-reference) for more programmatic broadcasting**.** The SDK provides easy-to-use TypeScript functions to construct transactions of all types and broadcast them to a blockchain node.

See [here for tutorials and examples.](common-snippets/creating-signing-and-broadcasting-txs.md)




# 🔃 Create and Submit Txs

To learn more about broadcasting transactions with Cosmos SDK, you can visit [https://docs.cosmos.network/v0.46/run-node/txs.html](https://docs.cosmos.network/v0.46/run-node/txs.html).



We recommend generating, signing, and broadcasting your transactions with the [BitBadges SDK](broken-reference). The SDK provides easy-to-use TypeScript functions to construct transactions of all types and broadcast them to a blockchain node.&#x20;

You can also run the BitBadges blockchain software and interact with its CLI, but this is a lot more complicated and does not have native EIP712 Ethereum support.

Another option for signing and broadcasting your transactions is to use  [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast). You will have to generate your message to JSON format, but you can then just copy and paste it. The gas, transaction signers, signing the transaction is all outsourced to the interface.&#x20;

See [here for tutorials and examples.](bitbadges-sdk/common-snippets/creating-signing-and-broadcasting-txs.md) &#x20;

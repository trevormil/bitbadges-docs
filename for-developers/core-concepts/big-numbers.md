# 🔢 Big Numbers

On the blockchain, we use Cosmos SDKs Uint class which can represent any integer of arbitrary value. This means that it can exceed standard uint or number types like Go's uint64 or Number.MAX\_SAFE\_INTEGER in JavaScript.

To combat this, we cannot use these standard number types that may lose precision and instead must use ones that will never lose precision.&#x20;

For the blockchain, all number types in submitted Msgs and transactions must be first stringified and then will be parsed as a Cosmos.Uint on-chain.

For the SDK and API, we use BigInts for the main functionality and stringify everything when sending over HTTP via the API.

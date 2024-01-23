# Experiences Protocol

Pre-Readings: [Experiences Protocol](../../overview/protocols/experiences-protocol.md)

Behind the scenes, the BitBadges experiences protocol is pretty simple.&#x20;

1. We store a map of addresses -> collectionIds on the BitBadges databases (via the on-chain protocols module) where the collectionIds is where to find the experience badges for that user. The protocol name is "Experiences Protocol". The corresponding collectionId must be created by the corresponding address, and each address can only specify one collectionId.&#x20;
2. Badge IDs 1-6 should be the following. In the future, we plan to add more.

<figure><img src="../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

We do not do anything fancy with the protocol itself, such as index it via the API like we do with the BitBadges Follow Protocol. You can get the protocol collection IDs from the API for a specific user and then query balances yourself. Or, if you want to index it yourself, go ahead and run your own indexer!

# 🤝 BitBadges Follow Protocol

Pre-Readings: [Follow Protocol](../overview/ecosystem/bitbadges-follow-protocol.md)

Behind the scenes, the BitBadges follow protocol is pretty simple.&#x20;

1. We store a map of addresses -> collectionIds on the BitBadges databases (we are looking to decentralize this map in the near future) where the collectionIds is where to find the follow badge for that user. The corresponding collectionId must be created by the corresponding address, and each address can only specify one collectionId.&#x20;
2. Whoever owns the Badge ID 1 of the corresponding collection is who is "followed".

To get follow details or update follow details, use the BitBadges API!

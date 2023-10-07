# Compatibility

* Badge and collection metadata should be hosted in the following JSON format
* ```typescript
  export interface Metadata<T extends NumberType> {
    name: string;
    description: string;
    image: string;
    time?: UintRange<T>;
    validFrom?: UintRange<T>;
    color?: string;
    category?: string;
    externalUrl?: string;
    tags?: string[];
  }
  ```
* If the badge metadata URI includes "{id}" anywhere in the URI, it is to be replaced dynamically by the corresponding badge ID number.
* If your collection uses the off-chain balances type, the URI of the offChainBalancesMetadata should point to a JSON file which is a map of valid cosmosAddresses -> [Balance](../for-developers/concepts/balances.md) objects.
* Claim Metadata
* See example: [https://bafybeige732y7k3sm5qhppzvams6wiggxhcf7f4w27dgri6eggpl2ydiua.ipfs.dweb.link/](https://bafybeige732y7k3sm5qhppzvams6wiggxhcf7f4w27dgri6eggpl2ydiua.ipfs.dweb.link/)

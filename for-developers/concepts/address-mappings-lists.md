# 📧 Address Mappings (Lists)

[AddressMappings](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/AddressMapping.html) are a powerful feature similar to UintRanges. They allow us to specify a list of addresses, identified by an ID. Lists are really simple because you do not need to deal with all the added complexity of tokens (badges) such as supplys, permissions, transferability, etc.&#x20;

These are invertible meaning we can create a mapping that includes all addresses EXCEPT some specified addresses. Or, we can create a mapping that includes ONLY some specified addresses.

```typescript
export interface AddressMapping {
  mappingId: string;

  addresses: string[];
  includeAddresses: boolean;

  uri: string; 
  customData: string;

  createdBy?: string;
}
```

### **Storage**

**On-Chain:** AddressMappings are permanent and not updatable once created, if stored on-chain. They can be used to efficiently define transferability on-chain since the same address mapping can be referenced across collections by their unique IDs.

For example, mapping "xyz" can only transfer to mapping "abc" initiated by the reserved "Manager" mapping.

**Off-Chain:** Address mappings can also be created off-chain through our indexer / API. These are updatable and deletable. However, this is a centralized solution and doesn't use the blockchain.



### **Reserved Address Mappings**

There are a couple IDs for AddressMappings that are reserved for efficient shorthand methods. To enable this, "\_" and ":" and "!" are not allowed in the ID.

* If prefixed with "!", it denotes to invert the address mapping (e.g. "!id123" inverts the "id123" address mapping)
* Any valid Cosmos (bech32) address is reserved as the mapping that ONLY includes that specific address.
* "Mint" specifies the "Mint" address only.
* "Burn" specifies the "Burn" address only.
* "AllWithoutMint" denotes all valid user addresses (excluding the "Mint" address)
* "AllWithMint" denotes all valid user addresses as well as the "Mint" address
* "Manager" will be reserved for a mapping that ONLY includes the current manager of the collection

### Examples

This is the mapping which includes all addresses except "cosmos123...."

```typescript
{
  "mappingId": "off-chain_abcdef",
  "addresses": ["cosmos123..."],
  "includeAddresses": false,
  "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
  "customData": "",
  "createdBy": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
}
```
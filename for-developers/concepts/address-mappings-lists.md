# 📧 Address Mappings (Lists)

[AddressMappings](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/AddressMapping.html) are a powerful feature similar to UintRanges. They allow us to specify a list of addresses, identified by a mappingId.

These are invertible meaning we can create a mapping that includes all addresses EXCEPT some specified addresses (includeAddresses = false). Or, we can create a mapping that includes ONLY some specified addresses (includeAddresses = true).

```typescript
export interface AddressMapping {
  mappingId: string;

  addresses: string[];
  includeAddresses: boolean;

  uri: string; 
  customData: string;
}
```

### **Storage**

**On-Chain:** AddressMappings are permanent and not updatable once created, if stored on-chain. These can be created using [MsgCreateAddressMappings](cosmos-msgs.md).

They can be used to define transferability on-chain. For example, mapping "xyz" can only transfer to mapping "abc" initiated by the reserved "Manager" mapping.

The same address mapping is not unique to a collection on-chain and can be used for defining transferability by any collection.

**Off-Chain:** Address mappings can also be created off-chain through our indexer / API. These are updatable and deletable. However, this is a centralized solution and doesn't use the blockchain. Everything is simply stored on our centralized servers

### **Reserved Address Mappings**

There are a couple IDs for AddressMappings that are reserved for efficient shorthand methods. To enable this, "\_" and ":" and "!" are not allowed anywhere in a standard ID.

* If prefixed with "!", it denotes to invert the address mapping (e.g. "!id123" inverts the "id123" address mapping)
* Any valid Cosmos (bech32) address is reserved as the mapping that ONLY includes that specific address.
* "Mint" specifies the "Mint" address only.
* "AllWithoutMint" denotes all valid user addresses (excluding the "Mint" address)
* "AllWithMint" denotes all valid user addresses as well as the "Mint" address
* "Manager" will be reserved for a mapping that ONLY includes the current manager of the collection. If the manager changes, this mapping will dynamically update to the new manager.

### Examples

This is the mapping which includes all addresses except "cosmos123...."

```typescript
{
  "mappingId": "abcdef",
  "addresses": ["cosmos123..."],
  "includeAddresses": false,
  "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
  "customData": "",
  "createdBy": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
}
```

# 📧 Address Mappings (Lists)

[AddressMappings](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/AddressMapping.html) are a powerful feature similar to UintRanges. They allow us to specify a list of addresses, identified by a mappingId.



```typescript
export interface AddressMapping {
  mappingId: string;

  addresses: string[];
  includeAddresses: boolean;

  uri: string; 
  customData: string;
}
```

### Inverting

These are invertible meaning we can create a mapping that includes all addresses EXCEPT some specified addresses (includeAddresses = false). Or, we can create a mapping that includes ONLY some specified addresses (includeAddresses = true).

**IMPORTANT:** When you invert, the inversion by default includes the "Mint" address. This is important when handling the **fromMapping** of approvals. You do not want to accidentally approve users to transfer from the "Mint" address.



### **Storage**

**On-Chain:** AddressMappings are permanent and not updatable once created, if stored on-chain. These can be created using [MsgCreateAddressMappings](cosmos-msgs.md).

They can be used to define transferability on-chain. For example, mapping "xyz" can only transfer to mapping "abc" initiated by the reserved "Manager" mapping.

The same address mapping is not unique to a collection on-chain and can be used for defining transferability by any collection.

**Off-Chain:** Address mappings can also be created off-chain through our indexer / API. These are updatable and deletable. However, this is a centralized solution and doesn't use the blockchain. Everything is simply stored on our centralized servers

### **Reserved Address Mapping IDs**

There are a couple IDs for AddressMappings that are reserved for efficient shorthand methods. To enable this, "\_" and ":" and "!" are not allowed anywhere in a standard ID.

* If prefixed with "!", it denotes to invert the address mapping (e.g. "!id123" inverts the "id123" address mapping)
* Any valid Cosmos (bech32) address is reserved as the mapping that ONLY includes that specific address.
* "Mint" specifies the "Mint" address only.
* "AllWithoutAddress1" denotes all valid user addresses excluding Address1 (e.g. "AllWithoutMint")
* "AllWithoutAddress1:Address2:Address3" denotes all valid user addresses excluding Address 1,2,and3 (e.g. "AllWithoutMint:cosmos123...:cosmos456...")
* "All" or "AllWithMint" denotes all valid user addresses as well as the "Mint" address

### Custom IDs

Reserved address mappings are provided for convenience, so you don't actually have to create an AddressMapping on-chain first. However, long mapping IDs are very inefficient, especially if used multiple times (e.g.  "AllWithoutMint:cosmos123...:cosmos456...").&#x20;

For efficiency, consider creating a mapping with a unique short ID and reference the mapping that way. You can create a mapping which is all addresses except Mint, cosmos123..., cosmos456... and identified by the ID "abc". Instead of repeating the long "AllWithoutMint:cosmos123...:cosmos456..." wherever the ID is needed, you can simply repeat "abc" which saves a lot of resources.

### Examples

This is the mapping which includes all addresses except "cosmos123...." and "cosmos456...."

```typescript
{
  "mappingId": "abcdef",
  "addresses": ["cosmos123...", "cosmos456...."],
  "includeAddresses": false,
  ...
}
```

<pre class="language-typescript"><code class="lang-typescript"><strong>//Snippet from getReservedAddressMapping() of the SDK
</strong><strong>
</strong><strong>if (addressMappingId === 'Mint') {
</strong>  addressMapping = {
    mappingId: 'Mint',
    addresses: ['Mint'],
    includeAddresses: true,
    ...
  };
}

if (addressMappingId === 'Manager') {
  addressMapping = {
    mappingId: 'Manager',
    addresses: [managerAddress],
    includeAddresses: true,
    ...
  };
}

if (addressMappingId === 'AllWithoutMint') {
  addressMapping = {
    mappingId: 'AllWithoutMint',
    addresses: ['Mint'],
    includeAddresses: false,
    ...
  };
}

if (addressMappingId === 'AllWithMint') {
  addressMapping = {
    mappingId: 'AllWithMint',
    addresses: [],
    includeAddresses: false,
    ...
  };
}

if (addressMappingId === 'None') {
  addressMapping = {
    mappingId: 'None',
    addresses: [],
    includeAddresses: true,
    ...
  };
}

if (convertToCosmosAddress(addressMappingId)) {
  addressMapping = {
    mappingId: addressMappingId,
    addresses: [addressMappingId],
    includeAddresses: true,
    ...
  };
}
</code></pre>

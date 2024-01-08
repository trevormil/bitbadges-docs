# 📧 Address Mappings (Lists)

[AddressMappings](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/AddressMapping.html) are a powerful feature with range logic similar to UintRanges. They allow us to specify a list of addresses, identified by a mappingId.

```typescript
export interface AddressMapping {
  mappingId: string;

  addresses: string[];
  includeAddresses: boolean;

  uri: string; 
  customData: string;
}
```

### Inverting (Whitelist vs Blacklist)

These are invertible meaning we can create a mapping that includes all addresses EXCEPT some specified addresses (includeAddresses = false). Or, we can create a mapping that includes ONLY some specified addresses (includeAddresses = true). More commonly, this is thought of as a blacklist or whitelist.

**IMPORTANT:** When you invert, the inversion by default includes the "Mint" address. This is important when handling the **fromMapping** of approvals. You do not want to accidentally approve users to transfer from the "Mint" address.

### **Storage**

**On-Chain:** AddressMappings are **permanent and not updatable** once created, if stored on-chain. These can be created using [MsgCreateAddressMappings](../create-and-broadcast-txs/cosmos-sdk-msgs/).

They can be used to define transferability on-chain. For example, mapping "xyz" can only transfer to mapping "abc" initiated by the reserved "Manager" mapping.

The same address mapping is not unique to a collection on-chain and can be used for defining transferability by any collection.

**Off-Chain:** Address mappings can also be created off-chain through our indexer / API. These are updatable and deletable, along with additional options. However, this is a centralized solution and doesn't use the blockchain. Everything is simply stored on our centralized servers



### **Reserved Address Mapping IDs**

There are a couple IDs for AddressMappings that are reserved for efficient shorthand methods. To enable this, "\_" and ":" and "!" are not allowed anywhere in a standard ID.

* "All" denotes all valid user addresses (as well as the "Mint" address which is important when specifying the sender mapping of transferability)
* Any valid Cosmos (bech32) address is reserved as the mapping that ONLY includes that specific address.
  * "Mint" specifies the "Mint" address only.
  * "cosmos1abc..." specified the mapping with "cosmos1abc...." only
* Combination shorthands
  * Using the ":" character, you can combine multiple addresses such as "Mint:cosmos1abc...". This would represent the mapping with Mint and cosmos1abc...
* Inversion shorthands
  *   If prefixed with "!", it denotes to invert the address mapping.&#x20;

      * "!id123" inverts the "id123" address mapping
      * "!Mint" inverts the Mint mapping
      *   "!Mint:cosmos1abc..." inverts the "Mint:cosmos1abc..." mapping which means all but the two specified addresses

          The above bullet may look a little weird to developers because it may only seem like the "Mint' is inverted but others aren't. You can also wrap everything in a parentheses such as "!(Mint:cosmos1abc...)"



See below for the function for generating them.

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

```typescript
function getReservedMapping(
  addressMappingId: string,
  allowAliases?: boolean,
): AddressMapping {
  let inverted = false
  let addressMapping: AddressMapping | undefined = undefined

  if (addressMappingId[0] === '!' && !addressMappingId.startsWith('!(')) {
    inverted = true
    addressMappingId = addressMappingId.slice(1)
  } else if (addressMappingId.startsWith('!(') && addressMappingId.endsWith(')')) {
    inverted = true
    addressMappingId = addressMappingId.slice(2, -1)
  }

  if (addressMappingId === 'Mint') {
    addressMapping = {
      mappingId: 'Mint',
      addresses: ['Mint'],
      includeAddresses: true,
      uri: '',
      customData: '',
      createdBy: '',
    }
  } else if (addressMappingId.startsWith('AllWithout')) {
    addressMapping = {
      mappingId: addressMappingId,
      addresses: [],
      includeAddresses: false,
      uri: '',
      customData: '',
      createdBy: '',
    }

    const addresses = addressMappingId.slice(10).split(':')

    for (let address of addresses) {
      addressMapping.addresses.push(address)
    }
  } else if (addressMappingId === 'AllWithMint' || addressMappingId === 'All') {
    addressMapping = {
      mappingId: addressMappingId,
      addresses: [],
      includeAddresses: false,
      uri: '',
      customData: '',
      createdBy: '',
    }
  } else if (addressMappingId === 'None') {
    addressMapping = {
      mappingId: 'None',
      addresses: [],
      includeAddresses: true,
      uri: '',
      customData: '',
      createdBy: '',
    }
  } else {
    //split by :
    const addressesToCheck = addressMappingId.split(':')
    let allAreValid = true
    //For tracker IDs, we allow aliasses(aka non valid addresses)
    if (!allowAliases) {
      for (let address of addressesToCheck) {
        if (address != 'Mint' && !convertToCosmosAddress(address)) {
          allAreValid = false
        }
      }
    }

    if (allAreValid) {
      addressMapping = {
        mappingId: addressMappingId,
        addresses: addressesToCheck,
        includeAddresses: true,
        uri: '',
        customData: '',
        createdBy: '',
      }
    }
  }

  if (inverted && addressMapping) {
    addressMapping.includeAddresses = !addressMapping.includeAddresses
  }

  if (!addressMapping) {
    throw new Error(`Invalid address mapping ID: ${addressMappingId}`)
  }

  return addressMapping
}

/**
 * Generates a mapping ID for a given address mapping.
 *
 * @param {AddressMapping} addressMapping - The address mapping to generate the ID for
 *
 * @category Address Mappings
 */
export const generateReservedMappingId = (
  addressMapping: AddressMapping,
): string => {
  let mappingId = ''

  // Logic to determine the mappingId based on the properties of addressMapping
  if (addressMapping.includeAddresses) {
    if (addressMapping.addresses.length > 0) {
      const addresses = addressMapping.addresses
        .map((x) => (isAddressValid(x) ? convertToCosmosAddress(x) : x))
        .join(':')
      mappingId = `${addresses}`
    } else {
      mappingId = 'None'
    }
  } else {
    if (addressMapping.addresses.length > 0) {
      const addresses = addressMapping.addresses
        .map((x) => (isAddressValid(x) ? convertToCosmosAddress(x) : x))
        .join(':')
      mappingId = `!(${addresses})`
    } else {
      mappingId = 'All'
    }
  }

  return mappingId
}
```

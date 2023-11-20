# Address Mappings

Below shows you how to use the **getReservedAddressMapping** function. Note this only allows you to fetch reserved address mappings (i.e.e "Mint", "Manager", "AllWithMint", etc). To fetch custom created ones, you will have to fetch them from the indexer, blockchain, or API.&#x20;



#### Tutorial: Fetching and Managing Reserved Address Mappings

**1. Introduction to AddressMapping**

The `AddressMapping` type allows for a flexible representation of a list of addresses, identified by a globally unique ID. It provides a robust system for including or excluding specific addresses and associating metadata through a URI. This is particularly useful for scenarios where a subset of addresses needs special treatment or where address metadata needs to be fetched from an external source.

**2. Fetching a Reserved Address Mapping**

To retrieve a specific reserved address mapping using its ID and a manager's address:

```typescript
const mappingId = "SomeReservedId"; // Replace with the actual ID
const managerAddr = "0xManagerAddress"; // Replace with the manager's address

const fetchedMapping = getReservedAddressMapping(mappingId, managerAddr);

if (fetchedMapping) {
  console.log(fetchedMapping);
} else {
  console.log("No mapping found for the given ID and manager address.");
}
```

**3. Understanding the AddressMapping Fields**

Once you've fetched an `AddressMapping`, you might want to process or display the data:

```typescript
if (fetchedMapping) {
  console.log(`Mapping ID: ${fetchedMapping.mappingId}`);
  console.log(`Addresses Count: ${fetchedMapping.addresses.length}`);
  console.log(`Include Only These Addresses: ${fetchedMapping.includeAddresses}`);
  console.log(`Metadata URI: ${fetchedMapping.uri}`);
  console.log(`Custom Data: ${fetchedMapping.customData}`);
  console.log(`Created By: ${fetchedMapping.createdBy}`);
}
```

**4. Making Use of the AddressMapping**

Based on the `includeAddresses` flag, you can determine whether the list represents addresses to include or exclude. This allows for flexibility in operations:

```typescript
if (fetchedMapping) {
  if (fetchedMapping.includeAddresses) {
    console.log("These addresses are included:");
  } else {
    console.log("All addresses except the following are included:");
  }

  fetchedMapping.addresses.forEach(address => {
    console.log(address);
  });
}
```

**Conclusion**

Using the `AddressMapping` interface and the `getReservedAddressMapping` function, you can manage sets of addresses efficiently. Whether you're fetching metadata, including or excluding specific addresses, or storing custom data on-chain, this system provides a comprehensive solution.

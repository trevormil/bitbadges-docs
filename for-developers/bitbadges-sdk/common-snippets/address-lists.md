# Address Lists

Below shows you how to use the **getReservedAddressList** function. Note this only allows you to fetch reserved address lists (i.e.e "Mint", "Manager", "!Mint", etc). To fetch custom created ones, you will have to fetch them from the indexer, blockchain, or API.

#### Tutorial: Fetching and Managing Reserved Address Lists

**1. Introduction to AddressList**

The `AddressList` type allows for a flexible representation of a list of addresses, identified by a globally unique ID. It provides a robust system for including or excluding specific addresses and associating metadata through a URI. This is particularly useful for scenarios where a subset of addresses needs special treatment or where address metadata needs to be fetched from an external source.

**2. Fetching a Reserved Address List**

To retrieve a specific reserved address list using its ID and a manager's address:

```typescript
const listId = "SomeReservedId"; // Replace with the actual ID

const fetchedList = getReservedAddressList(listId);

if (fetchedList) {
  console.log(fetchedList);
} else {
  console.log("No list found for the given ID and manager address.");
}
```

**3. Understanding the AddressList Fields**

Once you've fetched an `AddressList`, you might want to process or display the data:

```typescript
if (fetchedList) {
  console.log(`List ID: ${fetchedList.listId}`);
  console.log(`Addresses Count: ${fetchedList.addresses.length}`);
  console.log(`Include Only These Addresses: ${fetchedList.whitelist}`);
  console.log(`Metadata URI: ${fetchedList.uri}`);
  console.log(`Custom Data: ${fetchedList.customData}`);
  console.log(`Created By: ${fetchedList.createdBy}`);
}
```

**4. Making Use of the AddressList**

Based on the `whitelist` flag, you can determine whether the list represents addresses to include or exclude. This allows for flexibility in operations:

```typescript
if (fetchedList) {
  if (fetchedList.whitelist) {
    console.log("These addresses are included:");
  } else {
    console.log("All addresses except the following are included:");
  }

  fetchedList.addresses.forEach(address => {
    console.log(address);
  });
}
```

**Conclusion**

Using the `AddressList` interface and the `getReservedAddressList` function, you can manage sets of addresses efficiently. Whether you're fetching metadata, including or excluding specific addresses, or storing custom data on-chain, this system provides a comprehensive solution.

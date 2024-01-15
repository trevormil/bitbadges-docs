# 📧 Address Lists

[AddressLists](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/AddressList.html) are a powerful feature with range logic similar to UintRanges. They allow us to specify a list of addresses, identified by a listId.

```typescript
export interface AddressList {
  listId: string;

  addresses: string[];
  whitelist: boolean;

  uri: string; 
  customData: string;
}
```

### Using Lists

**Using Lists Instead of Badges**

Theoretically, address lists are just a simplified version of the badge interface. All address lists can be implemented as a badge where the list is determined by who has a positive balance of the badge.

However, sometimes, this simplicity may be desired, such as not dealing with balances, ownership times, permissions, etc. On the BitBadges site, we allow you to create both lists and badges, depending on your desired level of customization.

**Using Lists to Define Transferability**

When defining transferability and approvals, you need to define the list of addresses who can send, receive, and inititate the transfer. The AddressList interface is used for this on-chain.

### Inverting (Whitelist vs Blacklist)

These are invertible meaning we can create a list that includes all addresses EXCEPT some specified addresses (whitelist = false). Or, we can create a list that includes ONLY some specified addresses (whitelist = true). More commonly, this is thought of as a blacklist or whitelist.

**IMPORTANT:** When you invert, the inversion by default includes the "Mint" address. This is important when handling the **fromList** of approvals. You do not want to accidentally approve users to transfer from the "Mint" address.

### **Storage**

**On-Chain:** AddressLists are **permanent and not updatable** once created, if stored on-chain. These can be created using [MsgCreateAddressLists](../create-and-broadcast-txs/cosmos-sdk-msgs/).

They can be used to define transferability on-chain. For example, list "xyz" can only transfer to list "abc" initiated by the reserved "Manager" list.

The same address list is not unique to a collection on-chain and can be used for defining transferability by any collection.

**Off-Chain:** Address lists can also be created off-chain through our indexer / API. These are updatable and deletable, along with additional options. However, this is a centralized solution and doesn't use the blockchain. Everything is simply stored on our centralized servers

### **Reserved Address List IDs**

There are a couple IDs for AddressLists that are reserved for efficient shorthand methods. To enable this, "\_" and ":" and "!" are not allowed anywhere in a standard ID.

* "All" denotes all valid user addresses (as well as the "Mint" address which is important when specifying the sender list of transferability)
* Any valid Cosmos (bech32) address is reserved as the list that ONLY includes that specific address.
  * "Mint" specifies the "Mint" address only.
  * "cosmos1abc..." specified the list with "cosmos1abc...." only
* Combination shorthands
  * Using the ":" character, you can combine multiple addresses such as "Mint:cosmos1abc...". This would represent the list with Mint and cosmos1abc...
* Inversion shorthands
  * If prefixed with "!", it denotes to invert the address list.
    * "!id123" inverts the "id123" address list
    * "!Mint" inverts the Mint list
    *   "!Mint:cosmos1abc..." inverts the "Mint:cosmos1abc..." list which means all but the two specified addresses

        The above bullet may look a little weird to developers because it may only seem like the "Mint' is inverted but others aren't. You can also wrap everything in a parentheses such as "!(Mint:cosmos1abc...)"

Use the SDK functions below for generating IDs / lists.

```typescript
function getReservedList(
  addressListId: string, allowAliases?: boolean,
): AddressList

export const generateReservedListId = (
  addressList: AddressList,
): string
```

### Custom IDs

Reserved address lists are provided for convenience, so you don't actually have to create an AddressList on-chain first. However, long list IDs are very inefficient, especially if used multiple times (e.g. "AllWithoutMint:cosmos123...:cosmos456...").

For efficiency, consider creating a list with a unique short ID and reference the list that way. You can create a list which is all addresses except Mint, cosmos123..., cosmos456... and identified by the ID "abc". Instead of repeating the long "AllWithoutMint:cosmos123...:cosmos456..." wherever the ID is needed, you can simply repeat "abc" which saves a lot of resources.

### Examples

This is the list which includes all addresses except "cosmos123...." and "cosmos456...."

```typescript
{
  "listId": "abcdef",
  "addresses": ["cosmos123...", "cosmos456...."],
  "whitelist": false,
  ...
}
```

# Address Lists

[AddressLists](https://bitbadges.github.io/bitbadgesjs/interfaces/iAddressList.html) are a powerful feature with range logic similar to UintRanges. They allow us to specify a list of addresses, identified by a listId.

```typescript
export interface AddressList {
    listId: string;

    addresses: string[];
    whitelist: boolean;

    uri: string;
    customData: string;
}
```

### When to use lists?

**Using Lists Instead of Badges**

Theoretically, address lists are just a simplified version of the badge interface. Thus, all address lists can be implemented as a badge where the list is determined by who has a positive balance of the badge. However, sometimes, this simplicity may be desired, such as not dealing with balances, ownership times, permissions, etc. On the BitBadges site, we allow you to create both lists and badges, depending on your desired level of customization.

**Using Lists to Define Transferability / Approvals**

When defining transferability and approvals on-chain, you need to define the list of addresses who can send, receive, and initiate the transfer. The AddressList interface is reused for this on-chain.

We currently only support permanent lists on-chain vis the MsgCreateAddressList interface.

### Inverting (Whitelist vs Blacklist)

Lists are invertible meaning we can create a list that includes all addresses EXCEPT some specified addresses (whitelist = false). Or, we can create a list that includes ONLY some specified addresses (whitelist = true). More commonly, this is thought of as a blacklist or whitelist.

**IMPORTANT:** When you invert, the inversion by default includes the "Mint" address. This is important when handling the **fromList** of approvals. You do not want to accidentally approve users to transfer from the "Mint" address.

### **Storage**

**On-Chain:** AddressLists are **permanent and not updatable** once created, if stored on-chain. These can be created using [MsgCreateAddressLists](../bitbadges-blockchain/cosmos-sdk-msgs/). They can be used to define transferability on-chain. For example, those on list "xyz" can only transfer to list "abc" initiated by those on the reserved "AllWithoutMint" list.

An address list is not unique to a collection on-chain and can be used for defining transferability by any collection.

**Off-Chain:** Address lists can also be created off-chain through BitBadges. These are updatable and deletable, along with additional options (private? viewable with link? survey mode?). However, this is a centralized solution and doesn't use the blockchain. Everything is simply stored on our centralized servers.

### **Reserved Address List IDs**

There are a couple IDs for AddressLists that are reserved for efficient shorthand methods. To enable this, "\_" and ":" and "!" are not allowed anywhere in a standard ID.

* "All" denotes all valid user addresses (as well as the "Mint" address which is important when specifying the sender list of transferability)
* Any valid Cosmos (bech32) address is reserved as the list that ONLY includes that specific address.
  * "Mint" specifies the "Mint" address only.
  * "bb1abc..." specified the list with "bb1abc...." only
* Combination shorthands
  * Using the ":" character, you can combine multiple addresses such as "Mint:bb1abc...". This would represent the list with Mint and bb1abc...
* Inversion shorthands
  * If prefixed with "!", it denotes to invert the address list.
    * "!id123" inverts the "id123" address list
    * "!Mint" inverts the Mint list
    *   "!Mint:bb1abc..." inverts the "Mint:bb1abc..." list which means all but the two specified addresses

        The above bullet may look a little weird to developers because it may only seem like the "Mint' is inverted but others aren't. You can also wrap everything in a parentheses such as "!(Mint:bb1abc...)" if you would like

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

Reserved address lists are provided for convenience, so you don't actually have to create an AddressList on-chain everytime first. However, long list IDs are very inefficient, especially if used multiple times (e.g. "AllWithoutMint:bb123...:bb456...").

For efficiency, consider creating a list with a unique short reusable ID and reference the list that way, rather than the long ID. You can create a list which is all addresses except Mint, bb123..., bb456... and identified by the ID "abc". Instead of repeating the long "AllWithoutMint:bb123...:bb456..." wherever the ID is needed, you can simply repeat "abc" which saves a lot of resources.

### Examples

This is the list which includes all addresses except "bb123...." and "bb456...."

```typescript
{
  "listId": "abcdef",
  "addresses": ["bb123...", "bb456...."],
  "whitelist": false,
  ...
}
```

# 📧 Address Lists (Lists)

[AddressLists](https://bitbadges.github.io/bitbadgesjs/packages/proto/docs/interfaces/AddressList.html) are a powerful feature with range logic similar to UintRanges. They allow us to specify a list of addresses, identified by a listId.

```typescript
export interface AddressList {
  listId: string;

  addresses: string[];
  allowlist: boolean;

  uri: string; 
  customData: string;
}
```

### Inverting (Allowlist vs Blocklist)

These are invertible meaning we can create a list that includes all addresses EXCEPT some specified addresses (allowlist = false). Or, we can create a list that includes ONLY some specified addresses (allowlist = true). More commonly, this is thought of as a blocklist or allowlist.

**IMPORTANT:** When you invert, the inversion by default includes the "Mint" address. This is important when handling the **fromList** of approvals. You do not want to accidentally approve users to transfer from the "Mint" address.

### **Storage**

**On-Chain:** AddressLists are **permanent and not updatable** once created, if stored on-chain. These can be created using [MsgCreateAddressLists](../create-and-broadcast-txs/cosmos-sdk-msgs/).

They can be used to define transferability on-chain. For example, list "xyz" can only transfer to list "abc" initiated by the reserved "Manager" list.

The same address list is not unique to a collection on-chain and can be used for defining transferability by any collection.

**Off-Chain:** Address lists can also be created off-chain through our indexer / API. These are updatable and deletable, along with additional options. However, this is a centralized solution and doesn't use the blockchain. Everything is simply stored on our centralized servers



### **Reserved Address List IDs**

There are a couple IDs for AddressLists that are reserved for efficient shorthand methods. To enable this, "\_" and ":" and "!" are not allowed anywhere in a standard ID.

* If prefixed with "!", it denotes to invert the address list (e.g. "!id123" inverts the "id123" address list)
* Any valid Cosmos (bech32) address is reserved as the list that ONLY includes that specific address.
* "Mint" specifies the "Mint" address only.
* "AllWithoutAddress1" denotes all valid user addresses excluding Address1 (e.g. "AllWithoutMint")
* "AllWithoutAddress1:Address2:Address3" denotes all valid user addresses excluding Address 1,2,and3 (e.g. "AllWithoutMint:cosmos123...:cosmos456...")
* "All" or "AllWithMint" denotes all valid user addresses as well as the "Mint" address

See below for the function for generating them.

### Custom IDs

Reserved address lists are provided for convenience, so you don't actually have to create an AddressList on-chain first. However, long list IDs are very inefficient, especially if used multiple times (e.g.  "AllWithoutMint:cosmos123...:cosmos456...").&#x20;

For efficiency, consider creating a list with a unique short ID and reference the list that way. You can create a list which is all addresses except Mint, cosmos123..., cosmos456... and identified by the ID "abc". Instead of repeating the long "AllWithoutMint:cosmos123...:cosmos456..." wherever the ID is needed, you can simply repeat "abc" which saves a lot of resources.

### Examples

This is the list which includes all addresses except "cosmos123...." and "cosmos456...."

```typescript
{
  "listId": "abcdef",
  "addresses": ["cosmos123...", "cosmos456...."],
  "allowlist": false,
  ...
}
```

<pre class="language-typescript"><code class="lang-typescript"><a data-footnote-ref href="#user-content-fn-1">function</a> getReservedList(
  addressListId: string,
  allowAliases?: boolean,
): AddressList {
  let inverted = false
  let addressList: AddressList | undefined = undefined

  if (addressListId[0] === '!') {
    inverted = true
    addressListId = addressListId.slice(1)
  }

  if (addressListId === 'Mint') {
    addressList = {
      listId: 'Mint',
      addresses: ['Mint'],
      allowlist: true,
      uri: '',
      customData: '',
      createdBy: '',
    }
  } else if (addressListId.startsWith('AllWithout')) {
    addressList = {
      listId: addressListId,
      addresses: [],
      allowlist: false,
      uri: '',
      customData: '',
      createdBy: '',
    }

    const addresses = addressListId.slice(10).split(':')

    for (let address of addresses) {
      addressList.addresses.push(address)
    }
  } else if (addressListId === 'AllWithMint' || addressListId === 'All') {
    addressList = {
      listId: addressListId,
      addresses: [],
      allowlist: false,
      uri: '',
      customData: '',
      createdBy: '',
    }
  } else if (addressListId === 'None') {
    addressList = {
      listId: 'None',
      addresses: [],
      allowlist: true,
      uri: '',
      customData: '',
      createdBy: '',
    }
  } else {
    //split by :
    const addressesToCheck = addressListId.split(':')
    let allAreValid = true
    //For tracker IDs, we allow aliasses(aka non valid addresses)
    if (!allowAliases) {
      for (let address of addressesToCheck) {
        if (address != 'Mint' &#x26;&#x26; !convertToCosmosAddress(address)) {
          allAreValid = false
        }
      }
    }

    if (allAreValid) {
      addressList = {
        listId: addressListId,
        addresses: addressesToCheck,
        allowlist: true,
        uri: '',
        customData: '',
        createdBy: '',
      }
    }
  }

  if (inverted &#x26;&#x26; addressList) {
    addressList.allowlist = !addressList.allowlist
  }

  if (!addressList) {
    throw new Error(`Invalid address list ID: ${addressListId}`)
  }

  return addressList
}
</code></pre>

[^1]: 

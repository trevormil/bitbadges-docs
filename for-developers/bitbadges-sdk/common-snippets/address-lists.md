# Address Lists

AddressLists are the base type for lists. The BitBadgesAddressList class you see returned from the API extends this. Learn more about lists in the core concepts.

```typescript
const list = new AddressList({
    addresses: ['bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls'],
    whitelist: true,
    customData: '',
    uri: '',
    createdBy: 'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls',
    listId: 'abc123',
});

const isInList = list.checkAddress('bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls'); // true
const invertedList = list.toInverted();
const isInListNow = invertedList.checkAddress(
    'bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls'
); // false

list.remove('bb1kj9kt5y64n5a8677fhjqnmcc24ht2vy9atmdls');
const isEmpty = list.isEmpty();

const MintList = AddressList.Reserved('Mint');
const All = AddressList.AllAddresses();
```

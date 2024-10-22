# Address Lists

AddressLists are the base type for lists. The BitBadgesAddressList class you see returned from the API extends this. Learn more about lists in the core concepts.

```typescript
const list = new AddressList({
  addresses: ['bb1hsk6jryyqjfhp5g4g7j0qldj9nqdj0qc02fgmh'],
  whitelist: true,
  customData: '',
  uri: '',
  createdBy: 'bb1hsk6jryyqjfhp5g4g7j0qldj9nqdj0qc02fgmh',
  listId: 'abc123',
})

const isInList = list.checkAddress('bb1hsk6jryyqjfhp5g4g7j0qldj9nqdj0qc02fgmh') // true
const invertedList = list.toInverted()
const isInListNow = invertedList.checkAddress('bb1hsk6jryyqjfhp5g4g7j0qldj9nqdj0qc02fgmh') // false

list.remove('bb1hsk6jryyqjfhp5g4g7j0qldj9nqdj0qc02fgmh')
const isEmpty = list.isEmpty()


const MintList = AddressList.Reserved('Mint')
const All = AddressList.AllAddresses();
```

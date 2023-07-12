# 🎖 Collections

Collections are stored using the following interface.

```protobuf
message BadgeCollection {
    uint64 collectionId = 1;
    string collectionUri = 2; 
    repeated BadgeUri badgeUris = 3;
    string bytes = 4;
    uint64 manager = 5;
    uint64 permissions = 6;
    repeated TransferMapping disallowedTransfers = 7;
    repeated TransferMapping managerApprovedTransfers = 8;
    uint64 nextBadgeId = 9;
    repeated Balance unmintedSupplys = 10;
    repeated Balance maxSupplys = 11;
    repeated Claim claims = 12;
    uint64 standard = 13;
}
```

Collections have the following fields:

**collectionId** is a unique identifier for the badge collection, represented as a 64-bit unsigned integer. This is assigned by the chain itself. The first collection ID starts at 1 and increments by 1 each created collection.

**collectionUri** is a string that represents the URI where the collection metadata file can be found.  URI is max 100 characters. &#x20;

**badgeUris** is an array of **BadgeUri** objects, each containing a URI and an array of corresponding badge ID ranges, used to fetch the badge metadata.&#x20;

If "{id}" is included in the URI, each unique badge ID is to replace the "{id}" when fetching the metadata (e.g. https://bitbadges.io/{id} -> https://bitbadges.io/1 for badge ID 1). If not included, all badgeIds will point to the same URI.

```protobuf
message BadgeUri {
    string uri = 1;
    repeated IdRange badgeIds = 2;
}
```

**bytes** is a string that contains arbitrary bytes that can be used to store anything. The number of bytes is limited to 256. Should follow the rules according to the standard it implements.

**manager** is the account ID of the manager of the collection.

**permissions** is a 64-bit unsigned integer that represents the permissions set for the badge collection. The permissions are packed into an integer, with the bits corresponding to true/false for certain permissions. Leading zeroes must be applied. See [Permissions](permissions.md).

**disallowedTransfers** is an array of [TransferMapping](broken-reference) objects that defines the address combinations that cannot be transferred. This is used to freeze addresses and prevent transfers from specific (to, from) address pairs.&#x20;

If all addresses are disallowed, then the badge is non-transferable (see [Full Transfer Mappings](broken-reference)). If none are disallowed (i.e. the array is empty), the badge is transferable.

**managerApprovedTransfers** is an array of [**TransferMapping**](broken-reference) objects that defines which address combinations that the manager is approved to transfer to / from without approval.

This takes priority over the disallowedTransfers.&#x20;

This is often used to allow the manager to be able to forcefully revoke or burn badges.

**nextBadgeId** is a 64-bit unsigned integer that represents the next badge ID to be created. Starts at 1. Each badge created will increment this by 1. It cannot overflow.

**unmintedSupplys** is a [Balance ](broken-reference)map that stores the current unminted / undistributed badges by ID. Claimable badges are considered minted.

**maxSupplys** is a [Balance](broken-reference) map that stores the badge supplys by ID. Once created, badge supplys are final and cannot be edited.&#x20;

**claims** is an array of [Claim](claims.md) objects that defines how badges for this collection can be claimed.

**standard** is a 64-bit unsigned integer that represents the standard that the badge should implement. See [standards](standards.md).

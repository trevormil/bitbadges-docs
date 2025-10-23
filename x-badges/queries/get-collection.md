# GetCollection

Retrieves complete information about a collection.

## Proto Definition

```protobuf
message QueryGetCollectionRequest {
  string collectionId = 1; // ID of collection to retrieve
}

message QueryGetCollectionResponse {
  BadgeCollection collection = 1;
}

message BadgeCollection {
  string collectionId = 1; // Unique identifier for this collection
  repeated CollectionMetadataTimeline collectionMetadataTimeline = 2; // Collection metadata over time
  repeated BadgeMetadataTimeline badgeMetadataTimeline = 3; // Token metadata over time
  repeated CustomDataTimeline customDataTimeline = 4; // Arbitrary custom data over time
  repeated ManagerTimeline managerTimeline = 5; // Manager address over time
  CollectionPermissions collectionPermissions = 6; // Collection permissions
  repeated CollectionApproval collectionApprovals = 7; // Collection-level approvals
  repeated StandardsTimeline standardsTimeline = 8; // Standards over time
  repeated IsArchivedTimeline isArchivedTimeline = 9; // Archive status over time
  UserBalanceStore defaultBalances = 10; // Default balance store for users
  string createdBy = 11; // Creator of the collection
  repeated UintRange validBadgeIds = 12; // Valid token ID ranges
  string mintEscrowAddress = 13; // Generated escrow address for the collection
}

// See all the proto definitions [here](https://github.com/bitbadges/bitbadgeschain/tree/master/proto/badges)
```

## Usage Example

```bash
# CLI query
bitbadgeschaind query badges get-collection [id]

# REST API
curl "https://lcd.bitbadges.io/bitbadges/bitbadgeschain/badges/get_collection/1"
```

### Response Example

```json
{
    "collection": {
        "collectionId": "1"
        // ...
    }
}
```

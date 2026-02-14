# GetCollection

Retrieves complete information about a collection.

## Proto Definition

```protobuf
message QueryGetCollectionRequest {
  string collectionId = 1; // ID of collection to retrieve
}

message QueryGetCollectionResponse {
  TokenCollection collection = 1;
}

message TokenCollection {
  string collectionId = 1; // Unique identifier for this collection
  CollectionMetadata collectionMetadata = 2; // Collection metadata
  repeated TokenMetadata tokenMetadata = 3; // Token metadata
  string customData = 4; // Arbitrary custom data
  string manager = 5; // Manager address
  CollectionPermissions collectionPermissions = 6; // Collection permissions
  repeated CollectionApproval collectionApprovals = 7; // Collection-level approvals
  repeated string standards = 8; // Standards
  bool isArchived = 9; // Archive status
  UserBalanceStore defaultBalances = 10; // Default balance store for users
  string createdBy = 11; // Creator of the collection
  repeated UintRange validTokenIds = 12; // Valid token ID ranges
  string mintEscrowAddress = 13; // Generated escrow address for the collection
}

// See all the proto definitions [here](https://github.com/bitbadges/bitbadgeschain/tree/master/proto/tokenization)
```

## Usage Example

```bash
# CLI query
bitbadgeschaind query tokenization get-collection [id]

# REST API
curl "https://lcd.bitbadges.io/bitbadges/bitbadgeschain/tokenization/get_collection/1"
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

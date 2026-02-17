# Tokenization Precompile API Reference

**Address:** `0x0000000000000000000000000000000000001001`

Complete API documentation for the BitBadges tokenization precompile. All methods use a **JSON-based interface** where each method accepts a single `string calldata msgJson` parameter.

## Interface

```solidity
interface ITokenizationPrecompile {
    // Types
    struct MessageInput {
        string messageType;  // e.g., "createCollection", "transferTokens"
        string msgJson;      // JSON matching the protobuf format
    }
    
    // Transaction methods
    function transferTokens(string calldata msgJson) external returns (bool);
    function createCollection(string calldata msgJson) external returns (uint256);
    function updateCollection(string calldata msgJson) external returns (uint256);
    function deleteCollection(string calldata msgJson) external returns (bool);
    function createDynamicStore(string calldata msgJson) external returns (uint256);
    function updateDynamicStore(string calldata msgJson) external returns (bool);
    function deleteDynamicStore(string calldata msgJson) external returns (bool);
    function setDynamicStoreValue(string calldata msgJson) external returns (bool);
    function setIncomingApproval(string calldata msgJson) external returns (bool);
    function setOutgoingApproval(string calldata msgJson) external returns (bool);
    function deleteIncomingApproval(string calldata msgJson) external returns (bool);
    function deleteOutgoingApproval(string calldata msgJson) external returns (bool);
    function createAddressLists(string calldata msgJson) external returns (bool);
    function castVote(string calldata msgJson) external returns (bool);
    function executeMultiple(MessageInput[] calldata messages) external returns (bool success, bytes[] memory results);
    
    // Query methods
    function getCollection(string calldata msgJson) external view returns (bytes memory);
    function getBalance(string calldata msgJson) external view returns (bytes memory);
    function getBalanceAmount(string calldata msgJson) external view returns (uint256);
    function getTotalSupply(string calldata msgJson) external view returns (uint256);
    function getDynamicStore(string calldata msgJson) external view returns (bytes memory);
    function getDynamicStoreValue(string calldata msgJson) external view returns (bytes memory);
    function getAddressList(string calldata msgJson) external view returns (bytes memory);
}
```

## Transaction Methods

### `transferTokens`

Transfer tokens from the caller to one or more recipients.

**Signature:**
```solidity
function transferTokens(string calldata msgJson) external returns (bool success)
```

**JSON Format:**
```json
{
  "collectionId": "123",
  "transfers": [
    {
      "from": "bb1sender...",
      "toAddresses": ["bb1recipient..."],
      "balances": [
        {
          "amount": "1000",
          "tokenIds": [{"start": "1", "end": "1"}],
          "ownershipTimes": [{"start": "1", "end": "18446744073709551615"}]
        }
      ],
      "prioritizedApprovals": [],
      "onlyCheckPrioritizedCollectionApprovals": false,
      "onlyCheckPrioritizedIncomingApprovals": false,
      "onlyCheckPrioritizedOutgoingApprovals": false
    }
  ]
}
```

**Helper Function:**
```solidity
string memory json = TokenizationJSONHelpers.transferTokensJSON(
    collectionId,
    fromAddress,       // Sender address (Cosmos format)
    toAddresses,       // Recipient addresses array
    balancesJson       // Use balanceToJson helper
);
```

**Example:**
```solidity
string memory balancesJson = TokenizationJSONHelpers.balanceToJson(
    amount,
    TokenizationJSONHelpers.uintRangeToJson(1, 1),           // tokenIds
    TokenizationJSONHelpers.uintRangeToJson(1, type(uint64).max)  // ownershipTimes
);

string[] memory recipients = new string[](1);
recipients[0] = "bb1recipient...";

string memory transferJson = TokenizationJSONHelpers.transferTokensJSON(
    collectionId,
    "bb1sender...",    // from address
    recipients,
    balancesJson
);

bool success = TOKENIZATION.transferTokens(transferJson);
```

---

### `createCollection`

Create a new token collection.

**Signature:**
```solidity
function createCollection(string calldata msgJson) external returns (uint256 collectionId)
```

**JSON Format:**
```json
{
  "validTokenIds": [{"start": "1", "end": "1000"}],
  "manager": "bb1abc...",
  "collectionMetadata": {
    "uri": "ipfs://...",
    "customData": "{\"name\":\"My Token\"}"
  },
  "defaultBalances": {
    "autoApproveSelfInitiatedOutgoingTransfers": true,
    "autoApproveSelfInitiatedIncomingTransfers": true,
    "balances": []
  },
  "standards": ["ERC-3643"],
  "isArchived": false
}
```

**Helper Function:**
```solidity
string memory json = TokenizationJSONHelpers.createCollectionJSON(
    validTokenIdsJson,        // Use uintRangeToJson or uintRangeArrayToJson
    manager,                   // Cosmos address string
    collectionMetadataJson,   // Use collectionMetadataToJson
    defaultBalancesJson,      // Use simpleUserBalanceStoreToJson or custom JSON
    collectionPermissionsJson, // "{}" for empty
    standardsJson,             // Use stringArrayToJson
    customData,                // Optional string
    isArchived                 // bool
);
```

**Example:**
```solidity
string memory validTokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(1, 1000);
string memory metadataJson = TokenizationJSONHelpers.collectionMetadataToJson(
    "ipfs://metadata",
    "{\"name\":\"My Token\"}"
);
string memory defaultBalancesJson = TokenizationJSONHelpers.simpleUserBalanceStoreToJson(
    true, true, false
);
string[] memory standards = new string[](1);
standards[0] = "ERC-3643";
string memory standardsJson = TokenizationJSONHelpers.stringArrayToJson(standards);

string memory createJson = TokenizationJSONHelpers.createCollectionJSON(
    validTokenIdsJson,
    _addressToString(address(this)),
    metadataJson,
    defaultBalancesJson,
    "{}",
    standardsJson,
    "",
    false
);

uint256 collectionId = TOKENIZATION.createCollection(createJson);
```

---

### `createDynamicStore`

Create a dynamic boolean store (e.g., KYC registry).

**Signature:**
```solidity
function createDynamicStore(string calldata msgJson) external returns (uint256 storeId)
```

**JSON Format:**
```json
{
  "defaultValue": false,
  "uri": "ipfs://store-metadata",
  "customData": "{\"type\":\"kyc\"}"
}
```

**Helper Function:**
```solidity
string memory json = TokenizationJSONHelpers.createDynamicStoreJSON(
    defaultValue,  // bool
    uri,           // string
    customData     // string
);
```

**Example:**
```solidity
string memory createJson = TokenizationJSONHelpers.createDynamicStoreJSON(
    false,
    "ipfs://kyc-registry",
    "{\"type\":\"kyc\"}"
);

uint256 storeId = TOKENIZATION.createDynamicStore(createJson);
```

---

### `setDynamicStoreValue`

Set a value in a dynamic store for an address.

**Signature:**
```solidity
function setDynamicStoreValue(string calldata msgJson) external returns (bool success)
```

**JSON Format:**
```json
{
  "storeId": "123",
  "address": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "value": true
}
```

**Helper Function:**
```solidity
string memory json = TokenizationJSONHelpers.setDynamicStoreValueJSON(
    storeId,  // uint256
    address_, // address
    value     // bool
);
```

**Example:**
```solidity
string memory setValueJson = TokenizationJSONHelpers.setDynamicStoreValueJSON(
    kycRegistryId,
    user,
    true
);

TOKENIZATION.setDynamicStoreValue(setValueJson);
```

---

### `executeMultiple`

Execute multiple messages sequentially in a single atomic transaction. This is particularly useful for workflows like "Create Collection + Transfer Tokens".

**Signature:**
```solidity
function executeMultiple(MessageInput[] calldata messages) external returns (bool success, bytes[] memory results)
```

**MessageInput Structure:**
```solidity
struct MessageInput {
    string messageType;  // Message type identifier (e.g., "createCollection", "transferTokens")
    string msgJson;      // JSON string matching the protobuf format for the message type
}
```

**Supported Message Types:**
All transaction methods are supported. See [README](README.md) for the complete list.

**Example:**
```solidity
ITokenizationPrecompile.MessageInput[] memory messages = new ITokenizationPrecompile.MessageInput[](2);

// Message 1: Create Collection
string memory createJson = TokenizationJSONHelpers.createCollectionJSON(...);
messages[0] = ITokenizationPrecompile.MessageInput({
    messageType: "createCollection",
    msgJson: createJson
});

// Message 2: Transfer Tokens (using collectionId = 0 for auto-prev)
string memory transferJson = TokenizationJSONHelpers.transferTokensJSON(
    0,  // collectionId = 0 means "use previous collection" (auto-prev)
    recipients,
    amount,
    tokenIdsJson,
    ownershipJson
);
messages[1] = ITokenizationPrecompile.MessageInput({
    messageType: "transferTokens",
    msgJson: transferJson
});

// Execute both messages atomically
(bool success, bytes[] memory results) = TOKENIZATION.executeMultiple(messages);
require(success, "Multi-message execution failed");

// Decode results
uint256 collectionId = abi.decode(results[0], (uint256));
bool transferSuccess = abi.decode(results[1], (bool));
```

**Key Features:**
- **Atomic Execution**: All messages succeed or all fail (no partial execution)
- **Sequential Order**: Messages execute in the order provided
- **Auto-Prev Support**: Use `collectionId: "0"` in JSON to reference the collection created in a previous message
- **Result Decoding**: Results are returned as `bytes[]` - decode based on message type:
  - Boolean results: `abi.decode(results[i], (bool))`
  - Uint256 results: `abi.decode(results[i], (uint256))`

**Error Handling:**
If any message fails, the entire transaction is rolled back and an error is returned with context about which message failed (index and type).

**Gas Considerations:**
- Base gas: 10,000
- Per message overhead: 1,000 gas units per message
- Total gas = base + (per message × count) + sum of individual message gas costs

---

### `deleteIncomingApproval` / `deleteOutgoingApproval`

Delete an approval by ID.

**Signature:**
```solidity
function deleteIncomingApproval(string calldata msgJson) external returns (bool success)
function deleteOutgoingApproval(string calldata msgJson) external returns (bool success)
```

**JSON Format:**
```json
{
  "collectionId": "123",
  "approvalId": "approval-123"
}
```

**Helper Functions:**
```solidity
string memory json = TokenizationJSONHelpers.deleteIncomingApprovalJSON(
    collectionId,
    approvalId
);

string memory json = TokenizationJSONHelpers.deleteOutgoingApprovalJSON(
    collectionId,
    approvalId
);
```

---

### `deleteCollection`

Delete a collection (only creator can delete).

**Signature:**
```solidity
function deleteCollection(string calldata msgJson) external returns (bool success)
```

**JSON Format:**
```json
{
  "collectionId": "123"
}
```

**Helper Function:**
```solidity
string memory json = TokenizationJSONHelpers.deleteCollectionJSON(collectionId);
```

---

## Query Methods

### `getCollection`

Get collection details by ID.

**Signature:**
```solidity
function getCollection(string calldata msgJson) external view returns (bytes memory collection)
```

**JSON Format:**
```json
{
  "collectionId": "123"
}
```

**Helper Function:**
```solidity
string memory json = TokenizationJSONHelpers.getCollectionJSON(collectionId);
```

**Example:**
```solidity
string memory queryJson = TokenizationJSONHelpers.getCollectionJSON(collectionId);
bytes memory collection = TOKENIZATION.getCollection(queryJson);
// Decode off-chain using TypeScript SDK
```

---

### `getBalanceAmount`

Get the exact balance amount for a specific (tokenId, ownershipTime) combination. Returns `uint256` directly.

This function queries a single token ID and single ownership time, returning the exact balance amount the user owns. For querying multiple token IDs or time ranges, use `getBalance` instead and process the full balance store off-chain.

**Signature:**
```solidity
function getBalanceAmount(string calldata msgJson) external view returns (uint256 amount)
```

**JSON Format:**
```json
{
  "collectionId": "123",
  "address": "bb1...",
  "tokenId": "1",
  "ownershipTime": "1609459200000"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `collectionId` | string | Collection ID (uint as string) |
| `address` | string | User address (bech32 or 0x hex) |
| `tokenId` | string | Single token ID to query (uint as string) |
| `ownershipTime` | string | Single ownership time to query (uint as string, typically ms timestamp) |

**Example:**
```solidity
// Build JSON manually or use a helper
string memory balanceJson = string(abi.encodePacked(
    '{"collectionId":"', Strings.toString(collectionId),
    '","address":"', userAddress,
    '","tokenId":"', Strings.toString(tokenId),
    '","ownershipTime":"', Strings.toString(block.timestamp * 1000),
    '"}'
));

uint256 balance = TOKENIZATION.getBalanceAmount(balanceJson);
```

**Note:** For complex balance queries involving ranges, use `getBalance` to retrieve the full `UserBalanceStore` and calculate amounts off-chain.

---

### `getTotalSupply`

Get the total supply for a specific (tokenId, ownershipTime) combination. Returns `uint256` directly.

This function queries the total minted supply for a single token ID and ownership time.

**Signature:**
```solidity
function getTotalSupply(string calldata msgJson) external view returns (uint256 amount)
```

**JSON Format:**
```json
{
  "collectionId": "123",
  "tokenId": "1",
  "ownershipTime": "1609459200000"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `collectionId` | string | Collection ID (uint as string) |
| `tokenId` | string | Single token ID to query (uint as string) |
| `ownershipTime` | string | Single ownership time to query (uint as string, typically ms timestamp) |

**Example:**
```solidity
string memory supplyJson = string(abi.encodePacked(
    '{"collectionId":"', Strings.toString(collectionId),
    '","tokenId":"', Strings.toString(tokenId),
    '","ownershipTime":"', Strings.toString(block.timestamp * 1000),
    '"}'
));

uint256 supply = TOKENIZATION.getTotalSupply(supplyJson);
```

---

### `getDynamicStoreValue`

Get a value from a dynamic store. Returns protobuf-encoded `bytes`.

**Signature:**
```solidity
function getDynamicStoreValue(string calldata msgJson) external view returns (bytes memory value)
```

**JSON Format:**
```json
{
  "storeId": "123",
  "address": "bb1..."
}
```

**Helper Function:**
```solidity
string memory json = TokenizationJSONHelpers.getDynamicStoreValueJSON(
    storeId,
    userAddress
);
```

**Example:**
```solidity
string memory getValueJson = TokenizationJSONHelpers.getDynamicStoreValueJSON(
    kycRegistryId,
    user
);

bytes memory result = TOKENIZATION.getDynamicStoreValue(getValueJson);
if (result.length == 0) return false;
return abi.decode(result, (bool));  // For boolean stores
```

---

### `getAddressList`

Get an address list by ID.

**Signature:**
```solidity
function getAddressList(string calldata msgJson) external view returns (bytes memory list)
```

**JSON Format:**
```json
{
  "listId": "my-list-id"
}
```

**Helper Function:**
```solidity
string memory json = TokenizationJSONHelpers.getAddressListJSON(listId);
```

---

## Helper Library Reference

### Range Helpers

#### `uintRangeToJson(uint256 start, uint256 end) → string`

Convert a single range to JSON array format.

```solidity
string memory json = TokenizationJSONHelpers.uintRangeToJson(1, 100);
// Returns: [{"start":"1","end":"100"}]
```

#### `uintRangeArrayToJson(uint256[] memory starts, uint256[] memory ends) → string`

Convert multiple ranges to JSON array format.

```solidity
uint256[] memory starts = new uint256[](2);
uint256[] memory ends = new uint256[](2);
starts[0] = 1; ends[0] = 100;
starts[1] = 200; ends[1] = 300;

string memory json = TokenizationJSONHelpers.uintRangeArrayToJson(starts, ends);
// Returns: [{"start":"1","end":"100"},{"start":"200","end":"300"}]
```

### Collection Helpers

#### `collectionMetadataToJson(string memory uri, string memory customData) → string`

Build collection metadata JSON.

```solidity
string memory json = TokenizationJSONHelpers.collectionMetadataToJson(
    "ipfs://metadata",
    "{\"name\":\"My Token\"}"
);
```

#### `simpleUserBalanceStoreToJson(bool outgoing, bool incoming, bool allIncoming) → string`

Build simple default balances JSON with auto-approve flags.

```solidity
string memory json = TokenizationJSONHelpers.simpleUserBalanceStoreToJson(
    true,   // autoApproveSelfInitiatedOutgoingTransfers
    true,   // autoApproveSelfInitiatedIncomingTransfers
    false   // autoApproveAllIncomingTransfers
);
```

#### `stringArrayToJson(string[] memory strings) → string`

Convert string array to JSON array.

```solidity
string[] memory standards = new string[](2);
standards[0] = "ERC-3643";
standards[1] = "Security Token";
string memory json = TokenizationJSONHelpers.stringArrayToJson(standards);
// Returns: ["ERC-3643","Security Token"]
```

### Utility Helpers

#### `uintToString(uint256 value) → string`

Convert uint256 to string.

```solidity
string memory str = TokenizationJSONHelpers.uintToString(123);
// Returns: "123"
```

## JSON Format Reference

All JSON must match the protobuf JSON format:

- **Numbers**: Always strings (e.g., `"123"` not `123`)
- **Booleans**: `true` or `false` (not strings)
- **Arrays**: Standard JSON array format
- **Objects**: Standard JSON object format
- **Addresses**: Hex format with `0x` prefix (e.g., `"0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb"`)

## Error Handling

Invalid JSON or missing required fields will cause the transaction to revert. See [Error Handling](errors.md) for complete error codes and handling strategies.

## Security Notes

1. The `creator` field is **automatically** set from `msg.sender` and cannot be specified
2. All addresses are validated before processing
3. JSON is validated against protobuf schema
4. Invalid operations revert with clear error messages

## See Also

- [Complete README](README.md) - Overview and examples
- [Error Handling](errors.md) - Error codes and handling
- [Gas Costs](gas.md) - Gas calculation and optimization
- [Security](security.md) - Security best practices

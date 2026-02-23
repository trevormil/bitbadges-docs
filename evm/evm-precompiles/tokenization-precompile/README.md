# Tokenization Precompile

**Address:** `0x0000000000000000000000000000000000001001`

The Tokenization Precompile provides full access to the BitBadges tokenization module from Solidity contracts. All methods use a **JSON-based interface** for maximum flexibility and compatibility with the underlying Cosmos SDK protobuf messages.

## Quick Start

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./interfaces/ITokenizationPrecompile.sol";
import "./libraries/TokenizationJSONHelpers.sol";

contract MyTokenContract {
    ITokenizationPrecompile constant TOKENIZATION = 
        ITokenizationPrecompile(0x0000000000000000000000000000000000001001);
    
    // Transfer tokens using JSON helper
    function transfer(
        uint256 collectionId,
        address to,
        uint256 amount,
        uint256 tokenId
    ) external returns (bool) {
        address[] memory recipients = new address[](1);
        recipients[0] = to;
        
        // Build JSON using helpers
        string memory tokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(tokenId, tokenId);
        string memory ownershipTimesJson = TokenizationJSONHelpers.uintRangeToJson(1, type(uint256).max);
        
        string memory transferJson = TokenizationJSONHelpers.transferTokensJSON(
            collectionId,
            recipients,
            amount,
            tokenIdsJson,
            ownershipTimesJson
        );
        
        return TOKENIZATION.transferTokens(transferJson);
    }
    
    // Query balance using JSON helper
    function balanceOf(
        uint256 collectionId,
        address user
    ) external view returns (uint256) {
        string memory tokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(1, type(uint256).max);
        string memory ownershipTimesJson = TokenizationJSONHelpers.uintRangeToJson(1, type(uint256).max);
        
        string memory balanceJson = TokenizationJSONHelpers.getBalanceAmountJSON(
            collectionId,
            user,
            tokenIdsJson,
            ownershipTimesJson
        );
        
        return TOKENIZATION.getBalanceAmount(balanceJson);
    }
}
```

## Why JSON?

The precompile uses JSON strings instead of Solidity structs because:

1. **Protobuf Compatibility**: Direct mapping to Cosmos SDK protobuf messages
2. **Flexibility**: Easy to extend without breaking changes
3. **Type Safety**: JSON helpers provide compile-time safety
4. **Simplicity**: Single parameter per method, easy to understand

## Core Concepts

### 1. All Methods Accept JSON Strings

Every precompile method accepts a single `string calldata msgJson` parameter that matches the protobuf JSON format:

```solidity
// ✅ Correct - JSON string
string memory json = TokenizationJSONHelpers.transferTokensJSON(...);
bool success = TOKENIZATION.transferTokens(json);

// ❌ Wrong - struct parameters (old interface)
TOKENIZATION.transferTokens(collectionId, recipients, amount, tokenIds, ownershipTimes);
```

### 2. Use Helper Libraries

The `TokenizationJSONHelpers` library provides type-safe functions to construct JSON strings:

```solidity
import "./libraries/TokenizationJSONHelpers.sol";

// Simple operations
string memory json = TokenizationJSONHelpers.getCollectionJSON(collectionId);
TokenCollection memory collection = TOKENIZATION.getCollection(json);

// Complex operations with ranges
string memory tokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(1, 100);
string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(block.timestamp, expiration);
```

### 3. UintRange Arrays

Token IDs and ownership times use `UintRange` arrays. Convert them to JSON using helpers:

```solidity
// Single range
string memory singleRange = TokenizationJSONHelpers.uintRangeToJson(start, end);

// Multiple ranges
uint256[] memory starts = new uint256[](2);
uint256[] memory ends = new uint256[](2);
starts[0] = 1; ends[0] = 100;
starts[1] = 200; ends[1] = 300;
string memory multiRange = TokenizationJSONHelpers.uintRangeArrayToJson(starts, ends);
```

## Common Patterns

### Pattern 1: Simple Token Transfer

```solidity
function transferToken(
    uint256 collectionId,
    address to,
    uint256 amount,
    uint256 tokenId
) external returns (bool) {
    address[] memory recipients = new address[](1);
    recipients[0] = to;
    
    // Full ownership (no expiration)
    string memory tokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(tokenId, tokenId);
    string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(1, type(uint256).max);
    
    string memory transferJson = TokenizationJSONHelpers.transferTokensJSON(
        collectionId,
        recipients,
        amount,
        tokenIdsJson,
        ownershipJson
    );
    
    return TOKENIZATION.transferTokens(transferJson);
}
```

### Pattern 2: Time-Bound Transfer

```solidity
function transferWithExpiration(
    uint256 collectionId,
    address to,
    uint256 amount,
    uint256 tokenId,
    uint256 expirationTime
) external returns (bool) {
    address[] memory recipients = new address[](1);
    recipients[0] = to;
    
    // Time-bound ownership
    string memory tokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(tokenId, tokenId);
    string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(
        block.timestamp,
        expirationTime
    );
    
    string memory transferJson = TokenizationJSONHelpers.transferTokensJSON(
        collectionId,
        recipients,
        amount,
        tokenIdsJson,
        ownershipJson
    );
    
    return TOKENIZATION.transferTokens(transferJson);
}
```

### Pattern 3: Create Dynamic Store (KYC Registry)

```solidity
uint256 public kycRegistryId;

function initializeKYCRegistry() external {
    string memory createJson = TokenizationJSONHelpers.createDynamicStoreJSON(
        false,  // defaultValue: not KYC'd by default
        "ipfs://kyc-registry-metadata",
        "{\"type\":\"kyc\"}"
    );
    
    kycRegistryId = TOKENIZATION.createDynamicStore(createJson);
}

function setKYCStatus(address user, bool isKYCd) external {
    string memory setValueJson = TokenizationJSONHelpers.setDynamicStoreValueJSON(
        kycRegistryId,
        user,
        isKYCd
    );
    
    TOKENIZATION.setDynamicStoreValue(setValueJson);
}

function isKYCVerified(address user) external view returns (bool) {
    string memory getValueJson = TokenizationJSONHelpers.getDynamicStoreValueJSON(
        kycRegistryId,
        user
    );
    
    DynamicStoreValueResult memory result = TOKENIZATION.getDynamicStoreValue(getValueJson);
    // Use the field that matches your store's value type (e.g. for a boolean store, the struct's bool field)
    return result.verified;  // field name depends on your DynamicStoreValueResult definition
}
}
```

### Pattern 4: Create Collection

```solidity
function createMyCollection(
    string memory name,
    string memory symbol
) external returns (uint256) {
    // Build JSON components
    string memory validTokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(1, 1000);
    
    string memory defaultBalancesJson = TokenizationJSONHelpers.simpleUserBalanceStoreToJson(
        true,   // autoApproveSelfInitiatedOutgoingTransfers
        true,   // autoApproveSelfInitiatedIncomingTransfers
        false   // autoApproveAllIncomingTransfers
    );
    
    string memory metadataJson = TokenizationJSONHelpers.collectionMetadataToJson(
        "ipfs://collection-metadata",
        string(abi.encodePacked("{\"name\":\"", name, "\",\"symbol\":\"", symbol, "\"}"))
    );
    
    string[] memory standards = new string[](1);
    standards[0] = "ERC-3643";
    string memory standardsJson = TokenizationJSONHelpers.stringArrayToJson(standards);
    
    // Build complete JSON
    string memory createJson = TokenizationJSONHelpers.createCollectionJSON(
        validTokenIdsJson,
        _addressToString(address(this)),  // manager
        metadataJson,
        defaultBalancesJson,
        "{}",  // collectionPermissions (empty)
        standardsJson,
        "",    // customData
        false  // isArchived
    );
    
    return TOKENIZATION.createCollection(createJson);
}
```

### Pattern 5: Multi-Message Execution (Create Collection + Transfer Tokens)

Execute multiple operations atomically in a single transaction:

```solidity
function createAndTransfer(
    string memory name,
    address recipient,
    uint256 amount
) external returns (uint256 collectionId) {
    // Prepare messages array
    ITokenizationPrecompile.MessageInput[] memory messages = new ITokenizationPrecompile.MessageInput[](2);
    
    // Message 1: Create Collection
    string memory validTokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(1, 1000);
    string memory defaultBalancesJson = TokenizationJSONHelpers.simpleUserBalanceStoreToJson(true, true, false);
    string memory metadataJson = TokenizationJSONHelpers.collectionMetadataToJson(
        "ipfs://metadata",
        string(abi.encodePacked("{\"name\":\"", name, "\"}"))
    );
    string[] memory standards = new string[](0);
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
    
    messages[0] = ITokenizationPrecompile.MessageInput({
        messageType: "createCollection",
        msgJson: createJson
    });
    
    // Message 2: Transfer Tokens (using collectionId = 0 for auto-prev)
    address[] memory recipients = new address[](1);
    recipients[0] = recipient;
    string memory tokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(1, 1);
    string memory ownershipJson = TokenizationJSONHelpers.uintRangeToJson(1, type(uint256).max);
    
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
    
    // Decode collectionId from first result
    collectionId = abi.decode(results[0], (uint256));
    
    // Verify transfer succeeded (second result is bool)
    bool transferSuccess = abi.decode(results[1], (bool));
    require(transferSuccess, "Transfer failed");
    
    return collectionId;
}
```

## Available Methods

### Transaction Methods

All transaction methods return `bool` (success) or `uint256` (for creation methods):

- `transferTokens(string calldata msgJson) → bool`
- `createCollection(string calldata msgJson) → uint256`
- `updateCollection(string calldata msgJson) → uint256`
- `deleteCollection(string calldata msgJson) → bool`
- `createDynamicStore(string calldata msgJson) → uint256`
- `setDynamicStoreValue(string calldata msgJson) → bool`
- `setIncomingApproval(string calldata msgJson) → bool`
- `setOutgoingApproval(string calldata msgJson) → bool`
- `deleteIncomingApproval(string calldata msgJson) → bool`
- `deleteOutgoingApproval(string calldata msgJson) → bool`
- `createAddressLists(string calldata msgJson) → bool`
- `castVote(string calldata msgJson) → bool`
- `executeMultiple(MessageInput[] calldata messages) → (bool success, bytes[] memory results)` - Execute multiple messages atomically

### Multi-Message Execution

The `executeMultiple` method allows executing multiple tokenization messages in a single atomic transaction. This is particularly useful for workflows like "Create Collection + Transfer Tokens":

```solidity
ITokenizationPrecompile.MessageInput[] memory messages = new ITokenizationPrecompile.MessageInput[](2);

messages[0] = ITokenizationPrecompile.MessageInput({
    messageType: "createCollection",
    msgJson: createCollectionJson
});

messages[1] = ITokenizationPrecompile.MessageInput({
    messageType: "transferTokens",
    msgJson: transferTokensJson
});

(bool success, bytes[] memory results) = TOKENIZATION.executeMultiple(messages);
require(success, "Execution failed");

// Decode results
uint256 collectionId = abi.decode(results[0], (uint256));
bool transferSuccess = abi.decode(results[1], (bool));
```

**Key Features:**
- **Atomic Execution**: All messages succeed or all fail (no partial execution)
- **Sequential Order**: Messages execute in the order provided
- **Dynamic Routing**: Message type string determines which handler to use
- **Result Decoding**: Results are returned as `bytes[]` - decode based on message type

See [API Reference](API.md) for complete `executeMultiple` documentation.

### Query Methods

Query methods return structs (ABI-encoded; define matching struct types in your contract) or `uint256` for amount/supply queries:

- `getCollection(string calldata msgJson) → TokenCollection`
- `getBalance(string calldata msgJson) → UserBalanceStore`
- `getBalanceAmount(string calldata msgJson) → uint256`
- `getTotalSupply(string calldata msgJson) → uint256`
- `getCollectionStats(string calldata msgJson) → CollectionStats` - Get holder count and circulating supply
- `getDynamicStore(string calldata msgJson) → DynamicStore`
- `getDynamicStoreValue(string calldata msgJson) → DynamicStoreValueResult`
- `getAddressList(string calldata msgJson) → AddressList`

See [API Reference](API.md) for complete method documentation.

### Struct Return Types

Query methods (except `getBalanceAmount` and `getTotalSupply`) return ABI-encoded structs. Define struct types in your contract that match the precompile’s response layout:

```solidity
// Define matching struct types
struct CollectionStats {
    uint256 holderCount;
    Balance[] balances;
}

// Interface with struct return types
interface ITokenizationPrecompile {
    function getCollectionStats(string calldata msgJson)
        external view returns (CollectionStats memory);
}

// Usage - access fields directly in contract logic
CollectionStats memory stats = ITokenizationPrecompile(TOKENIZATION_ADDRESS)
    .getCollectionStats(queryJson);
uint256 holders = stats.holderCount;
```

See [API Reference - Struct Return Types](API.md#struct-return-types) for supported struct types and layout.

## Helper Library Functions

The `TokenizationJSONHelpers` library provides functions for all common operations:

### Transfer Helpers
- `transferTokensJSON(...)` - Build transfer JSON
- `uintRangeToJson(start, end)` - Single range
- `uintRangeArrayToJson(starts[], ends[])` - Multiple ranges

### Collection Helpers
- `createCollectionJSON(...)` - Build collection creation JSON
- `collectionMetadataToJson(uri, customData)` - Build metadata JSON
- `simpleUserBalanceStoreToJson(...)` - Build default balances JSON
- `stringArrayToJson(strings[])` - Convert string array to JSON

### Dynamic Store Helpers
- `createDynamicStoreJSON(defaultValue, uri, customData)` - Build store creation JSON
- `setDynamicStoreValueJSON(storeId, address, value)` - Build set value JSON
- `getDynamicStoreValueJSON(storeId, address)` - Build get value JSON

### Query Helpers
- `getCollectionJSON(collectionId)` - Build collection query JSON
- `getBalanceJSON(collectionId, userAddress)` - Build balance query JSON
- `getBalanceAmountJSON(...)` - Build balance amount query JSON
- `getTotalSupplyJSON(...)` - Build supply query JSON

### Utility Helpers
- `uintToString(value)` - Convert uint256 to string
- `deleteCollectionJSON(collectionId)` - Build delete JSON
- `deleteIncomingApprovalJSON(collectionId, approvalId)` - Build delete approval JSON
- `deleteOutgoingApprovalJSON(collectionId, approvalId)` - Build delete approval JSON

### Multi-Message Helpers
When using `executeMultiple`, build individual message JSONs using the helpers above, then construct the `MessageInput` array in Solidity.

## Return Value Handling

### Direct Returns (uint256)

Some methods return `uint256` directly:

```solidity
uint256 balance = TOKENIZATION.getBalanceAmount(balanceJson);
uint256 supply = TOKENIZATION.getTotalSupply(supplyJson);
uint256 collectionId = TOKENIZATION.createCollection(createJson);
```

### Struct Returns (Query Methods)

Query methods (e.g. `getCollection`, `getBalance`, `getCollectionStats`, `getDynamicStore`, `getDynamicStoreValue`, `getAddressList`) return ABI-encoded structs. Define matching struct types in your contract and use an interface that declares those return types; then use the returned struct fields directly in your logic. See [Struct Return Types](API.md#struct-return-types) for supported types and examples.

## Security Considerations

1. **Automatic Creator Field**: The `creator` field is automatically set from `msg.sender` and cannot be spoofed
2. **JSON Validation**: Invalid JSON will revert with clear error messages
3. **Type Safety**: Use helper functions to ensure correct JSON structure
4. **Gas Optimization**: JSON construction is efficient, but cache complex JSON strings when possible

## Best Practices

### 1. Always Use Helper Functions

```solidity
// ✅ Good - Type-safe and readable
string memory json = TokenizationJSONHelpers.transferTokensJSON(...);

// ❌ Bad - Error-prone manual construction
string memory json = string(abi.encodePacked('{"collectionId":"', ...));
```

### 2. Cache Complex JSON

```solidity
// ✅ Good - Cache for reuse
string memory tokenIdsJson = TokenizationJSONHelpers.uintRangeToJson(1, 1000);
// Use tokenIdsJson multiple times

// ❌ Bad - Reconstruct every time
// Rebuilding JSON on every call wastes gas
```

### 3. Validate Inputs Before Building JSON

```solidity
function transfer(uint256 collectionId, address to, uint256 amount) external {
    require(collectionId > 0, "Invalid collection");
    require(to != address(0), "Invalid recipient");
    require(amount > 0, "Invalid amount");
    
    // Now build JSON
    string memory json = TokenizationJSONHelpers.transferTokensJSON(...);
    TOKENIZATION.transferTokens(json);
}
```

### 4. Handle Errors Gracefully

```solidity
bool success = TOKENIZATION.transferTokens(transferJson);
if (!success) {
    // Handle failure - check events or revert with custom error
    revert TransferFailed();
}
```

### 5. Multi-Message Execution Best Practices

When using `executeMultiple`, follow these guidelines:

```solidity
// ✅ Good - Validate inputs before building messages
function createAndTransfer(
    string memory name,
    address recipient,
    uint256 amount
) external returns (uint256 collectionId) {
    require(bytes(name).length > 0, "Name required");
    require(recipient != address(0), "Invalid recipient");
    require(amount > 0, "Amount must be positive");
    
    ITokenizationPrecompile.MessageInput[] memory messages = new ITokenizationPrecompile.MessageInput[](2);
    
    // Build messages
    messages[0] = ITokenizationPrecompile.MessageInput({
        messageType: "createCollection",
        msgJson: createCollectionJson
    });
    
    messages[1] = ITokenizationPrecompile.MessageInput({
        messageType: "transferTokens",
        msgJson: transferTokensJson
    });
    
    // Execute atomically
    (bool success, bytes[] memory results) = TOKENIZATION.executeMultiple(messages);
    require(success, "Multi-message execution failed");
    
    // Decode and validate results
    require(results.length == 2, "Unexpected result count");
    collectionId = abi.decode(results[0], (uint256));
    require(collectionId > 0, "Invalid collection ID");
    
    bool transferSuccess = abi.decode(results[1], (bool));
    require(transferSuccess, "Transfer failed");
    
    return collectionId;
}
```

**Key Points:**
- Validate all inputs before building messages
- Use auto-prev (`collectionId: "0"`) to reference previous message results
- Always check result count matches message count
- Decode and validate each result
- Handle errors with clear messages

## Examples

See the example contracts for complete implementations:

- **[CarbonCreditToken](../contracts/examples/CarbonCreditToken.sol)** - Carbon credit tracking with vintages
- **[TwoFactorSecurityToken](../contracts/examples/TwoFactorSecurityToken.sol)** - 2FA-protected security tokens
- **[RealEstateSecurityToken](../contracts/examples/RealEstateSecurityToken.sol)** - ERC-3643 compliant real estate tokens
- **[PrivateEquityToken](../contracts/examples/PrivateEquityToken.sol)** - Private equity fund tokens

## Next Steps

- Read the [Complete API Reference](API.md) for all methods
- Learn about [Error Handling](errors.md)
- Understand [Gas Costs](gas.md)
- Review [Security Best Practices](security.md)
- Explore [Example Contracts](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/contracts/examples)

## Resources

- [Setup & Configuration](../setup-and-configuration.md)
- [Developer Guide](../developer-guide.md)
- [Architecture](../architecture.md)
- [Tokenization Module Documentation](../../../x-tokenization/README.md)

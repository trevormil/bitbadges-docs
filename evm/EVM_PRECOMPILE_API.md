# BitBadges EVM Precompile API Documentation

## Overview

The BitBadges EVM precompile provides a comprehensive interface for interacting with the tokenization module from Solidity smart contracts. The precompile is available at address `0x0000000000000000000000000000000000001001`.

## Table of Contents

- [Security](#security)
- [Type System](#type-system)
- [Transaction Methods](#transaction-methods)
- [Query Methods](#query-methods)
- [Examples](#examples)
- [Error Codes](#error-codes)

## Security

### Creator Field Security

**CRITICAL**: The `creator` field in all transaction methods is **automatically** set from `msg.sender`. This field is **NOT** exposed in the ABI and cannot be specified by calling contracts. This prevents impersonation attacks.

All transaction methods use `contract.Caller()` which returns the EVM `msg.sender` - the address that directly called the current contract. This cannot be spoofed.

### Best Practices

1. **Never trust `creator` parameters** - They don't exist in the ABI
2. **Always validate inputs** - Use the helper library validation functions
3. **Check return values** - All methods return success indicators
4. **Handle errors** - Use structured error codes for debugging

## Type System

All types are defined in `TokenizationTypes.sol` and mirror the proto message definitions 1:1. Use `BadgesHelpers.sol` for constructing and validating types.

### Core Types

- `UintRange`: Range of IDs (start, end inclusive)
- `Balance`: Token balance with amount, ownership times, and token IDs
- `CollectionMetadata`: Collection-level metadata
- `TokenMetadata`: Token-level metadata
- `UserBalanceStore`: Complete user balance and approval state
- `CollectionPermissions`: Collection-level permissions
- `UserPermissions`: User-level permissions

See `contracts/types/TokenizationTypes.sol` for complete type definitions.

## Transaction Methods

### Collection Management

#### `createCollection`
Creates a new token collection.

```solidity
function createCollection(
    TokenizationTypes.MsgCreateCollection calldata msg_
) external returns (uint256 collectionId);
```

**Parameters:**
- `msg_.defaultBalances`: Initial user balance store (can be empty)
- `msg_.validTokenIds`: Array of valid token ID ranges
- `msg_.collectionPermissions`: Collection permissions
- `msg_.manager`: Manager address
- `msg_.collectionMetadata`: Collection metadata
- `msg_.tokenMetadata`: Array of token metadata entries
- `msg_.customData`: Custom data string
- `msg_.collectionApprovals`: Array of collection approvals
- `msg_.standards`: Array of standard strings
- `msg_.isArchived`: Whether collection is archived

**Returns:** `uint256` - The created collection ID

**Example:**
```solidity
import {TokenizationTypes} from "./types/TokenizationTypes.sol";
import {BadgesHelpers} from "./libraries/BadgesHelpers.sol";

TokenizationTypes.MsgCreateCollection memory msg_;
msg_.validTokenIds = BadgesHelpers.createUintRangeArray(
    new uint256[](1),
    new uint256[](1)
);
msg_.validTokenIds[0] = BadgesHelpers.createUintRange(1, 100);
msg_.collectionMetadata = BadgesHelpers.createCollectionMetadata("", "");
msg_.collectionPermissions = BadgesHelpers.createEmptyCollectionPermissions();
msg_.manager = address(0x123...);
msg_.isArchived = false;

uint256 collectionId = tokenizationPrecompile.createCollection(msg_);
```

#### `updateCollection`
Updates an existing collection with update flags.

```solidity
function updateCollection(
    TokenizationTypes.MsgUpdateCollection calldata msg_
) external returns (uint256 collectionId);
```

#### `deleteCollection`
Deletes a collection (only creator can delete).

```solidity
function deleteCollection(uint256 collectionId) external returns (bool);
```

#### `setManager`
Sets the manager address for a collection.

```solidity
function setManager(
    uint256 collectionId,
    string calldata manager
) external returns (uint256 collectionId);
```

#### `setCollectionMetadata`
Sets collection-level metadata.

```solidity
function setCollectionMetadata(
    uint256 collectionId,
    string calldata uri,
    string calldata customData
) external returns (uint256 collectionId);
```

#### `setStandards`
Sets standards for a collection.

```solidity
function setStandards(
    uint256 collectionId,
    string[] calldata standards
) external returns (uint256 collectionId);
```

#### `setCustomData`
Sets custom data for a collection.

```solidity
function setCustomData(
    uint256 collectionId,
    string calldata customData
) external returns (uint256 collectionId);
```

#### `setIsArchived`
Sets the archived status of a collection.

```solidity
function setIsArchived(
    uint256 collectionId,
    bool isArchived
) external returns (uint256 collectionId);
```

### Token Management

#### `setValidTokenIds`
Sets valid token IDs for a collection.

```solidity
function setValidTokenIds(
    uint256 collectionId,
    TokenizationTypes.UintRange[] calldata validTokenIds,
    TokenizationTypes.TokenIdsActionPermission[] calldata canUpdateValidTokenIds
) external returns (uint256 collectionId);
```

#### `setTokenMetadata`
Sets metadata for specific token IDs.

```solidity
function setTokenMetadata(
    uint256 collectionId,
    TokenizationTypes.TokenMetadata[] calldata tokenMetadata,
    TokenizationTypes.TokenIdsActionPermission[] calldata canUpdateTokenMetadata
) external returns (uint256 collectionId);
```

### Transfers

#### `transferTokens`
Transfers tokens from the caller to one or more recipients.

```solidity
function transferTokens(
    uint256 collectionId,
    address[] calldata toAddresses,
    uint256 amount,
    TokenizationTypes.UintRange[] calldata tokenIds,
    TokenizationTypes.UintRange[] calldata ownershipTimes
) external returns (bool);
```

**Example:**
```solidity
address[] memory recipients = new address[](2);
recipients[0] = address(0xABC...);
recipients[1] = address(0xDEF...);

TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
tokenIds[0] = BadgesHelpers.createUintRange(1, 10);

TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
ownershipTimes[0] = BadgesHelpers.createFullOwnershipTimeRange();

bool success = tokenizationPrecompile.transferTokens(
    collectionId,
    recipients,
    100,
    tokenIds,
    ownershipTimes
);
```

### Approvals

#### `setOutgoingApproval`
Sets an outgoing approval for the caller.

```solidity
function setOutgoingApproval(
    uint256 collectionId,
    TokenizationTypes.UserOutgoingApproval calldata approval
) external returns (bool);
```

#### `setIncomingApproval`
Sets an incoming approval for the caller.

```solidity
function setIncomingApproval(
    uint256 collectionId,
    TokenizationTypes.UserIncomingApproval calldata approval
) external returns (bool);
```

#### `deleteOutgoingApproval`
Deletes an outgoing approval.

```solidity
function deleteOutgoingApproval(
    uint256 collectionId,
    string calldata approvalId
) external returns (bool);
```

#### `deleteIncomingApproval`
Deletes an incoming approval.

```solidity
function deleteIncomingApproval(
    uint256 collectionId,
    string calldata approvalId
) external returns (bool);
```

#### `updateUserApprovals`
Updates user approvals with update flags.

```solidity
function updateUserApprovals(
    TokenizationTypes.MsgUpdateUserApprovals calldata msg_
) external returns (bool);
```

#### `setCollectionApprovals`
Sets collection-level approvals.

```solidity
function setCollectionApprovals(
    uint256 collectionId,
    TokenizationTypes.CollectionApproval[] calldata collectionApprovals,
    TokenizationTypes.CollectionApprovalPermission[] calldata canUpdateCollectionApprovals
) external returns (uint256 collectionId);
```

#### `purgeApprovals`
Purges expired or specified approvals.

```solidity
function purgeApprovals(
    uint256 collectionId,
    bool purgeExpired,
    string calldata approverAddress,
    bool purgeCounterpartyApprovals,
    TokenizationTypes.ApprovalIdentifierDetails[] calldata approvalsToPurge
) external returns (uint256 numPurged);
```

### Dynamic Stores

#### `createDynamicStore`
Creates a dynamic key-value store.

```solidity
function createDynamicStore(
    bool defaultValue,
    string calldata uri,
    string calldata customData
) external returns (uint256 storeId);
```

#### `updateDynamicStore`
Updates a dynamic store.

```solidity
function updateDynamicStore(
    uint256 storeId,
    bool defaultValue,
    bool globalEnabled,
    string calldata uri,
    string calldata customData
) external returns (bool);
```

#### `deleteDynamicStore`
Deletes a dynamic store.

```solidity
function deleteDynamicStore(uint256 storeId) external returns (bool);
```

#### `setDynamicStoreValue`
Sets a value in a dynamic store for an address.

```solidity
function setDynamicStoreValue(
    uint256 storeId,
    address address_,
    bool value
) external returns (bool);
```

### Address Lists

#### `createAddressLists`
Creates one or more address lists.

```solidity
function createAddressLists(
    TokenizationTypes.AddressListInput[] calldata addressLists
) external returns (bool);
```

### Voting

#### `castVote`
Casts a vote on a proposal.

```solidity
function castVote(
    uint256 collectionId,
    string calldata approvalLevel,
    string calldata approverAddress,
    string calldata approvalId,
    string calldata proposalId,
    uint256 yesWeight
) external returns (bool);
```

## Query Methods

### Collections

#### `getCollection`
Queries a collection by ID. Returns structured `TokenCollection` type.

```solidity
function getCollection(
    uint256 collectionId
) external view returns (TokenizationTypes.TokenCollection memory);
```

**Example:**
```solidity
TokenizationTypes.TokenCollection memory collection = tokenizationPrecompile.getCollection(collectionId);
require(collection.collectionId == collectionId, "Collection not found");
string memory uri = collection.collectionMetadata.uri;
```

### Balances

#### `getBalance`
Queries a user's balance store. Returns structured `UserBalanceStore` type.

```solidity
function getBalance(
    uint256 collectionId,
    address userAddress
) external view returns (TokenizationTypes.UserBalanceStore memory);
```

**Example:**
```solidity
TokenizationTypes.UserBalanceStore memory balance = tokenizationPrecompile.getBalance(collectionId, userAddress);
uint256 numBalances = balance.balances.length;
if (numBalances > 0) {
    uint256 amount = balance.balances[0].amount;
}
```

#### `getBalanceAmount`
Queries the total balance amount for specific token IDs and ownership times.

```solidity
function getBalanceAmount(
    uint256 collectionId,
    address userAddress,
    TokenizationTypes.UintRange[] calldata tokenIds,
    TokenizationTypes.UintRange[] calldata ownershipTimes
) external view returns (uint256);
```

#### `getTotalSupply`
Queries the total supply for specific token IDs and ownership times.

```solidity
function getTotalSupply(
    uint256 collectionId,
    TokenizationTypes.UintRange[] calldata tokenIds,
    TokenizationTypes.UintRange[] calldata ownershipTimes
) external view returns (uint256);
```

### Address Lists

#### `getAddressList`
Queries an address list by ID. Returns structured `AddressList` type.

```solidity
function getAddressList(
    string calldata listId
) external view returns (TokenizationTypes.AddressList memory);
```

### Dynamic Stores

#### `getDynamicStore`
Queries a dynamic store by ID.

```solidity
function getDynamicStore(
    uint256 storeId
) external view returns (bytes memory);
```

#### `getDynamicStoreValue`
Queries a dynamic store value for an address.

```solidity
function getDynamicStoreValue(
    uint256 storeId,
    address userAddress
) external view returns (bytes memory);
```

### Approvals and Trackers

#### `getApprovalTracker`
Queries an approval tracker.

```solidity
function getApprovalTracker(
    uint256 collectionId,
    string calldata approvalLevel,
    address approverAddress,
    string calldata amountTrackerId,
    string calldata trackerType,
    address approvedAddress,
    string calldata approvalId
) external view returns (bytes memory);
```

#### `getChallengeTracker`
Queries a challenge tracker.

```solidity
function getChallengeTracker(
    uint256 collectionId,
    string calldata approvalLevel,
    address approverAddress,
    string calldata challengeTrackerId,
    uint256 leafIndex,
    string calldata approvalId
) external view returns (bytes memory);
```

#### `getETHSignatureTracker`
Queries an ETH signature tracker.

```solidity
function getETHSignatureTracker(
    uint256 collectionId,
    string calldata approvalLevel,
    address approverAddress,
    string calldata approvalId,
    string calldata challengeTrackerId,
    string calldata signature
) external view returns (bytes memory);
```

### Voting

#### `getVote`
Queries a single vote.

```solidity
function getVote(
    uint256 collectionId,
    string calldata approvalLevel,
    address approverAddress,
    string calldata approvalId,
    string calldata proposalId,
    address voterAddress
) external view returns (bytes memory);
```

#### `getVotes`
Queries all votes for a proposal.

```solidity
function getVotes(
    uint256 collectionId,
    string calldata approvalLevel,
    address approverAddress,
    string calldata approvalId,
    string calldata proposalId
) external view returns (bytes memory);
```

### Other Queries

#### `getWrappableBalances`
Queries wrappable balances for a denom and address.

```solidity
function getWrappableBalances(
    string calldata denom,
    address userAddress
) external view returns (uint256);
```

#### `isAddressReservedProtocol`
Checks if an address is a reserved protocol address.

```solidity
function isAddressReservedProtocol(address addr) external view returns (bool);
```

#### `getAllReservedProtocolAddresses`
Gets all reserved protocol addresses.

```solidity
function getAllReservedProtocolAddresses() external view returns (address[] memory);
```

#### `params`
Queries module parameters.

```solidity
function params() external view returns (bytes memory);
```

## Examples

### Complete Collection Creation Example

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./interfaces/ITokenizationPrecompile.sol";
import "./types/TokenizationTypes.sol";
import "./libraries/BadgesHelpers.sol";

contract MyTokenizationContract {
    ITokenizationPrecompile constant precompile = ITokenizationPrecompile(0x0000000000000000000000000000000000001001);
    
    function createMyCollection() external returns (uint256) {
        TokenizationTypes.MsgCreateCollection memory msg_;
        
        // Set valid token IDs (1-1000)
        msg_.validTokenIds = new TokenizationTypes.UintRange[](1);
        msg_.validTokenIds[0] = BadgesHelpers.createUintRange(1, 1000);
        
        // Set collection metadata
        msg_.collectionMetadata = BadgesHelpers.createCollectionMetadata(
            "https://example.com/metadata",
            "My custom data"
        );
        
        // Set empty permissions (anyone can manage)
        msg_.collectionPermissions = BadgesHelpers.createEmptyCollectionPermissions();
        
        // Set manager to this contract
        msg_.manager = address(this);
        
        // Set token metadata for tokens 1-100
        msg_.tokenMetadata = new TokenizationTypes.TokenMetadata[](1);
        msg_.tokenMetadata[0] = BadgesHelpers.createTokenMetadata(
            "https://example.com/token",
            "",
            BadgesHelpers.createUintRangeArray(
                new uint256[](1),
                new uint256[](1)
            )
        );
        msg_.tokenMetadata[0].tokenIds[0] = BadgesHelpers.createUintRange(1, 100);
        
        // Set standards
        msg_.standards = new string[](1);
        msg_.standards[0] = "ERC721";
        
        // Create collection
        return badges.createCollection(msg_);
    }
    
    function transferMyTokens(
        uint256 collectionId,
        address to,
        uint256 amount
    ) external {
        address[] memory recipients = new address[](1);
        recipients[0] = to;
        
        TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
        tokenIds[0] = BadgesHelpers.createUintRange(1, amount);
        
        TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
        ownershipTimes[0] = BadgesHelpers.createFullOwnershipTimeRange();
        
        badges.transferTokens(collectionId, recipients, amount, tokenIds, ownershipTimes);
    }
}
```

## Error Codes

The precompile uses structured error codes for consistent error handling:

- `ErrorCodeInvalidInput (1)`: Invalid input parameters
- `ErrorCodeCollectionNotFound (2)`: Collection not found
- `ErrorCodeBalanceNotFound (3)`: Balance not found
- `ErrorCodeTransferFailed (4)`: Transfer operation failed
- `ErrorCodeApprovalFailed (5)`: Approval operation failed
- `ErrorCodeQueryFailed (6)`: Query operation failed
- `ErrorCodeInternalError (7)`: Internal error

Errors are returned as `bytes` that can be decoded using the error code and message.

## Gas Costs

All methods have defined gas costs:
- Simple operations: 20,000 - 30,000 gas
- Complex operations (createCollection, updateCollection): 40,000 - 50,000 gas
- Query operations: 5,000 - 10,000 gas

Gas costs are automatically calculated by the precompile based on operation complexity.

## Helper Library

Use `BadgesHelpers.sol` for:
- Creating structs with validation
- Building default values
- Validating inputs
- Common range operations

**Example:**
```solidity
// Create a UintRange
TokenizationTypes.UintRange memory range = BadgesHelpers.createUintRange(1, 100);

// Create full ownership time range
TokenizationTypes.UintRange memory fullTime = BadgesHelpers.createFullOwnershipTimeRange();

// Create empty permissions
TokenizationTypes.CollectionPermissions memory perms = BadgesHelpers.createEmptyCollectionPermissions();

// Validate a range
require(BadgesHelpers.validateUintRange(range), "Invalid range");
```

## Migration Notes

### Return Type Changes

Query methods now return structured Solidity types instead of protobuf-encoded bytes:
- `getCollection`: Returns `TokenCollection` struct
- `getBalance`: Returns `UserBalanceStore` struct  
- `getAddressList`: Returns `AddressList` struct

This provides better type safety and IDE support. If you need the old bytes format, you can marshal the structs yourself.

## Support

For issues or questions:
- Check the type definitions in `TokenizationTypes.sol`
- Use `BadgesHelpers.sol` for common operations
- Review examples in this documentation
- See the implementation in `x/evm/precompiles/tokenization/`

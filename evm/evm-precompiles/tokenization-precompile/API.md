# Tokenization Precompile API Reference

**Address:** `0x0000000000000000000000000000000000001001`

Complete interface for interacting with the BitBadges tokenization module from Solidity contracts.

## Type System

All types are defined in `TokenizationTypes.sol` and mirror the Cosmos SDK proto definitions. Use `TokenizationHelpers.sol` for constructing types.

**Key Types:**
- `UintRange`: Range of IDs (start, end inclusive)
- `Balance`: Token balance with amount, ownership times, and token IDs
- `CollectionMetadata`: Collection-level metadata
- `TokenMetadata`: Token-level metadata
- `UserBalanceStore`: Complete user balance and approval state
- `CollectionPermissions`: Collection-level permissions

See `contracts/types/TokenizationTypes.sol` for complete definitions.

## Transaction Methods

### Collection Management

#### `createCollection`
Creates a new token collection. Returns the collection ID.

```solidity
function createCollection(
    TokenizationTypes.MsgCreateCollection calldata msg_
) external returns (uint256 collectionId);
```

#### `updateCollection`
Updates an existing collection.

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

#### `setManager`, `setCollectionMetadata`, `setStandards`, `setCustomData`, `setIsArchived`
Collection configuration methods.

### Token Management

#### `setValidTokenIds`
Sets valid token ID ranges for a collection.

#### `setTokenMetadata`
Sets metadata for specific token ID ranges.

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
address[] memory recipients = new address[](1);
recipients[0] = to;

TokenizationTypes.UintRange[] memory tokenIds = new TokenizationTypes.UintRange[](1);
tokenIds[0] = TokenizationHelpers.createSingleTokenIdRange(1);

TokenizationTypes.UintRange[] memory ownershipTimes = new TokenizationTypes.UintRange[](1);
ownershipTimes[0] = TokenizationHelpers.createFullOwnershipTimeRange();

precompile.transferTokens(collectionId, recipients, amount, tokenIds, ownershipTimes);
```

### Approvals

#### `setIncomingApproval`, `setOutgoingApproval`
Set incoming/outgoing approvals with complex criteria (merkle challenges, voting, time-based checks, etc.).

#### `deleteIncomingApproval`, `deleteOutgoingApproval`
Delete approvals by ID.

#### `updateUserApprovals`
Update user approvals with update flags.

#### `setCollectionApprovals`
Set collection-level approvals.

#### `purgeApprovals`
Purge expired or specified approvals.

### Dynamic Stores

#### `createDynamicStore`, `updateDynamicStore`, `deleteDynamicStore`
Manage dynamic key-value stores for flexible data storage.

#### `setDynamicStoreValue`
Set a value in a dynamic store for an address.

### Address Lists

#### `createAddressLists`
Create one or more address lists.

### Voting

#### `castVote`
Cast a vote on a proposal.

## Query Methods

### Collections

#### `getCollection`
Queries a collection by ID. Returns `TokenCollection` struct.

```solidity
function getCollection(
    uint256 collectionId
) external view returns (TokenizationTypes.TokenCollection memory);
```

### Balances

#### `getBalance`
Queries a user's complete balance store. Returns `UserBalanceStore` struct.

#### `getBalanceAmount`
Queries the total balance amount for specific token IDs and ownership times. Returns `uint256`.

```solidity
function getBalanceAmount(
    uint256 collectionId,
    address userAddress,
    TokenizationTypes.UintRange[] calldata tokenIds,
    TokenizationTypes.UintRange[] calldata ownershipTimes
) external view returns (uint256);
```

#### `getTotalSupply`
Queries the total supply for specific token IDs and ownership times. Returns `uint256`.

### Address Lists

#### `getAddressList`
Queries an address list by ID. Returns `AddressList` struct.

### Dynamic Stores

#### `getDynamicStore`, `getDynamicStoreValue`
Query dynamic stores and their values.

### Approvals and Trackers

#### `getApprovalTracker`, `getChallengeTracker`, `getETHSignatureTracker`
Query approval tracking data.

### Voting

#### `getVote`, `getVotes`
Query voting data.

### Other

#### `getWrappableBalances`
Queries wrappable balances for a denom and address.

#### `isAddressReservedProtocol`, `getAllReservedProtocolAddresses`
Protocol address utilities.

#### `params`
Queries module parameters.

## Helper Library

Use `TokenizationHelpers.sol` for constructing and validating types:

```solidity
// Create ranges
TokenizationTypes.UintRange memory range = TokenizationHelpers.createSingleTokenIdRange(1);
TokenizationTypes.UintRange memory fullTime = TokenizationHelpers.createFullOwnershipTimeRange();

// Create empty structures
TokenizationTypes.CollectionPermissions memory perms = 
    TokenizationHelpers.createEmptyCollectionPermissions();
```

## Security Note

The `creator` field in all transaction methods is **automatically** set from `msg.sender` and cannot be specified by calling contracts. This prevents impersonation attacks.

## Implementation Details

For detailed gas costs, error codes, and implementation specifics, see:
- [Gas Costs](gas.md)
- [Error Handling](errors.md)
- [Security](security.md)
- [Precompile Implementation](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/x/tokenization/precompile)

# Cosmos SDK Precompiles

These precompiles are provided by the upstream [cosmos/evm](https://github.com/cosmos/evm) module and provide direct access to Cosmos SDK functionality from EVM smart contracts.

Unlike BitBadges custom precompiles (which use JSON parameters), these precompiles use traditional ABI-encoded parameters.

## Address Overview

| Precompile | Address | Type |
|------------|---------|------|
| P256 | `0x0000000000000000000000000000000000000100` | Cryptography |
| Bech32 | `0x0000000000000000000000000000000000000400` | Utility |
| Staking | `0x0000000000000000000000000000000000000800` | Transactions + Queries |
| Distribution | `0x0000000000000000000000000000000000000801` | Transactions + Queries |
| ICS20 (IBC) | `0x0000000000000000000000000000000000000802` | Transactions + Queries |
| Vesting | `0x0000000000000000000000000000000000000803` | Transactions + Queries |
| Bank | `0x0000000000000000000000000000000000000804` | Queries Only |
| Governance | `0x0000000000000000000000000000000000000805` | Transactions + Queries |
| Slashing | `0x0000000000000000000000000000000000000806` | Transactions + Queries |

---

## P256 Precompile

**Address:** `0x0000000000000000000000000000000000000100`

Implements secp256r1 (P-256) signature verification per [EIP-7212](https://github.com/ethereum/RIPs/blob/master/RIPS/rip-7212.md).

**Usage:** Low-level call with 160 bytes input:
- Bytes 0-31: Hash of signed data
- Bytes 32-63: r component of signature
- Bytes 64-95: s component of signature
- Bytes 96-127: x coordinate of public key
- Bytes 128-159: y coordinate of public key

**Returns:** 32 bytes with value `1` if valid, empty if invalid.

**Gas:** 3,450

---

## Bech32 Precompile

**Address:** `0x0000000000000000000000000000000000000400`

Converts between hex addresses and bech32 addresses.

### Methods

```solidity
interface IBech32 {
    /// @notice Convert hex address to bech32 string
    function hexToBech32(address addr, string calldata prefix) external pure returns (string memory);

    /// @notice Convert bech32 string to hex address
    function bech32ToHex(string calldata bech32Addr) external pure returns (address);
}
```

**Example:**
```solidity
IBech32 bech32 = IBech32(0x0000000000000000000000000000000000000400);

// Convert EVM address to Cosmos address
string memory cosmosAddr = bech32.hexToBech32(msg.sender, "bb");
// Result: "bb1abc123..."

// Convert Cosmos address to EVM address
address evmAddr = bech32.bech32ToHex("bb1abc123...");
```

---

## Staking Precompile

**Address:** `0x0000000000000000000000000000000000000800`

Interact with the Cosmos SDK staking module.

### Transaction Methods

| Method | Description |
|--------|-------------|
| `createValidator(...)` | Create a new validator |
| `editValidator(...)` | Edit validator parameters |
| `delegate(address validator, uint256 amount)` | Delegate tokens to a validator |
| `undelegate(address validator, uint256 amount)` | Undelegate tokens from a validator |
| `redelegate(address srcValidator, address dstValidator, uint256 amount)` | Redelegate tokens between validators |
| `cancelUnbondingDelegation(address validator, uint256 amount, int64 creationHeight)` | Cancel an unbonding delegation |

### Query Methods

| Method | Description |
|--------|-------------|
| `delegation(address delegator, address validator)` | Get delegation info |
| `unbondingDelegation(address delegator, address validator)` | Get unbonding delegation info |
| `validator(address validator)` | Get validator info |
| `validators(string status, PageRequest pagination)` | Get all validators |
| `redelegation(address delegator, address srcValidator, address dstValidator)` | Get redelegation info |
| `redelegations(address delegator, address srcValidator, address dstValidator, PageRequest pagination)` | Get all redelegations |

---

## Distribution Precompile

**Address:** `0x0000000000000000000000000000000000000801`

Interact with the Cosmos SDK distribution module for staking rewards.

### Transaction Methods

| Method | Description |
|--------|-------------|
| `setWithdrawAddress(address withdrawAddr)` | Set the withdrawal address for rewards |
| `withdrawDelegatorRewards(address validator)` | Withdraw rewards from a validator |
| `withdrawValidatorCommission(address validator)` | Withdraw validator commission |
| `fundCommunityPool(Coin[] amount)` | Fund the community pool |
| `claimRewards(address delegator, uint32 maxRetrieve)` | Claim all rewards |
| `depositValidatorRewardsPool(address validator, Coin[] amount)` | Deposit to validator rewards pool |

### Query Methods

| Method | Description |
|--------|-------------|
| `validatorDistributionInfo(address validator)` | Get validator distribution info |
| `validatorOutstandingRewards(address validator)` | Get validator outstanding rewards |
| `validatorCommission(address validator)` | Get validator commission |
| `validatorSlashes(address validator, uint64 startingHeight, uint64 endingHeight, PageRequest pagination)` | Get validator slashes |
| `delegationRewards(address delegator, address validator)` | Get delegation rewards |
| `delegationTotalRewards(address delegator)` | Get total rewards for a delegator |
| `delegatorValidators(address delegator)` | Get validators for a delegator |
| `delegatorWithdrawAddress(address delegator)` | Get withdraw address |
| `communityPool()` | Get community pool balance |

---

## ICS20 (IBC Transfer) Precompile

**Address:** `0x0000000000000000000000000000000000000802`

Perform IBC token transfers.

### Transaction Methods

```solidity
interface IICS20 {
    /// @notice Transfer tokens via IBC
    function transfer(
        string calldata sourcePort,
        string calldata sourceChannel,
        string calldata denom,
        uint256 amount,
        string calldata receiver,
        Height calldata timeoutHeight,
        uint64 timeoutTimestamp,
        string calldata memo
    ) external returns (uint64 sequence);
}
```

### Query Methods

| Method | Description |
|--------|-------------|
| `denom(string hash)` | Get denom trace from hash |
| `denoms(PageRequest pagination)` | Get all denom traces |
| `denomHash(string trace)` | Get hash from denom trace |

---

## Vesting Precompile

**Address:** `0x0000000000000000000000000000000000000803`

Manage vesting accounts.

### Methods

| Method | Description |
|--------|-------------|
| `createClawbackVestingAccount(...)` | Create a clawback vesting account |
| `fundVestingAccount(...)` | Fund a vesting account |
| `clawback(...)` | Clawback unvested tokens |
| `updateVestingFunder(...)` | Update the funder address |
| `convertVestingAccount(...)` | Convert vesting to regular account |

---

## Bank Precompile

**Address:** `0x0000000000000000000000000000000000000804`

Query bank module data (read-only, no transactions).

### Query Methods

```solidity
interface IBank {
    /// @notice Get all balances for an account
    function balances(address account, PageRequest pagination) external view returns (Coin[] memory, PageResponse memory);

    /// @notice Get total supply of all tokens
    function totalSupply(PageRequest pagination) external view returns (Coin[] memory, PageResponse memory);

    /// @notice Get supply of a specific denom
    function supplyOf(string calldata denom) external view returns (Coin memory);
}
```

**Gas Costs:**
- `balances`: 2,851
- `totalSupply`: 2,477
- `supplyOf`: 2,477

---

## Governance Precompile

**Address:** `0x0000000000000000000000000000000000000805`

Interact with the Cosmos SDK governance module.

### Transaction Methods

| Method | Description |
|--------|-------------|
| `submitProposal(...)` | Submit a governance proposal |
| `deposit(uint64 proposalId, Coin[] amount)` | Deposit tokens to a proposal |
| `cancelProposal(uint64 proposalId)` | Cancel a proposal (proposer only) |
| `vote(uint64 proposalId, VoteOption option, string metadata)` | Vote on a proposal |
| `voteWeighted(uint64 proposalId, WeightedVoteOption[] options, string metadata)` | Vote with weights |

### Query Methods

| Method | Description |
|--------|-------------|
| `getVotes(uint64 proposalId, PageRequest pagination)` | Get votes for a proposal |
| `getVote(uint64 proposalId, address voter)` | Get a specific vote |
| `getDeposit(uint64 proposalId, address depositor)` | Get a deposit |
| `getDeposits(uint64 proposalId, PageRequest pagination)` | Get all deposits |
| `getTallyResult(uint64 proposalId)` | Get tally result |
| `getProposal(uint64 proposalId)` | Get proposal info |
| `getProposals(ProposalStatus status, address voter, address depositor, PageRequest pagination)` | Get all proposals |
| `getParams()` | Get governance params |
| `getConstitution()` | Get chain constitution |

---

## Slashing Precompile

**Address:** `0x0000000000000000000000000000000000000806`

Interact with the Cosmos SDK slashing module.

### Transaction Methods

```solidity
interface ISlashing {
    /// @notice Unjail a jailed validator
    function unjail(address validatorAddr) external;
}
```

### Query Methods

| Method | Description |
|--------|-------------|
| `getSigningInfo(address consAddr)` | Get signing info for a validator |
| `getSigningInfos(PageRequest pagination)` | Get all signing infos |
| `getParams()` | Get slashing params |

---

## Common Types

```solidity
struct Coin {
    string denom;
    uint256 amount;
}

struct PageRequest {
    bytes key;
    uint64 offset;
    uint64 limit;
    bool countTotal;
    bool reverse;
}

struct PageResponse {
    bytes nextKey;
    uint64 total;
}

struct Height {
    uint64 revisionNumber;
    uint64 revisionHeight;
}

enum VoteOption {
    Unspecified,
    Yes,
    Abstain,
    No,
    NoWithVeto
}
```

---

## Resources

- [Cosmos EVM Documentation](https://docs.cosmos.network/evm/v0.5.0/documentation/overview)
- [Cosmos EVM Precompiles Source](https://github.com/cosmos/evm/tree/v0.5.0/precompiles)

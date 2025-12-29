# MsgCastVote

Casts or updates a vote for a voting challenge in an approval criteria. Used in weighted quorum voting systems where multiple parties must vote to approve transfers.

Votes can be updated by casting a new vote with the same parameters, which overwrites the previous vote.

## Authorization

Only addresses in the voting challenge's `voters` list can cast votes. The `creator` address must match one of the voters in the challenge.

### Proto Definition

```protobuf
message MsgCastVote {
  option (cosmos.msg.v1.signer) = "creator";
  option (amino.name) = "badges/CastVote";

  // The address of the voter casting the vote.
  string creator = 1 [(cosmos_proto.scalar) = "cosmos.AddressString"];

  // The collection ID for the voting challenge.
  string collectionId = 2 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];

  // The approval level ("collection", "incoming", or "outgoing").
  string approvalLevel = 3;

  // The approver address (empty string for collection-level approvals).
  string approverAddress = 4;

  // The approval ID.
  string approvalId = 5;

  // The proposal ID (challenge ID) from the VotingChallenge.
  string proposalId = 6;

  // The percentage weight (0-100) allocated to "yes" vote.
  // The remaining percentage (100 - yesWeight) is allocated to "no" vote.
  // Example: yesWeight=70 means 70% yes, 30% no.
  string yesWeight = 7 [(gogoproto.customtype) = "Uint", (gogoproto.nullable) = false];
}

message MsgCastVoteResponse {}
```

### Usage Example

```bash
# CLI command
bitbadgeschaind tx badges cast-vote '[tx-json]' --from voter-key
```

#### JSON Example

```json
{
    "creator": "bb1voter123...",
    "collectionId": "1",
    "approvalLevel": "collection",
    "approverAddress": "",
    "approvalId": "multisig-approval",
    "proposalId": "proposal-1",
    "yesWeight": "100"
}
```

## Fields

| Field             | Type   | Description                                                                     |
| ----------------- | ------ | ------------------------------------------------------------------------------- |
| `creator`         | string | Voter address casting the vote (must be in challenge's voters list)             |
| `collectionId`    | string | Collection ID containing the approval                                           |
| `approvalLevel`   | string | Approval level: `"collection"`, `"incoming"`, or `"outgoing"`                   |
| `approverAddress` | string | Approver address (empty `""` for collection-level, user address for user-level) |
| `approvalId`      | string | ID of the approval containing the voting challenge                              |
| `proposalId`      | string | Proposal ID from the `VotingChallenge`                                          |
| `yesWeight`       | string | Percentage weight (0-100) for "yes" vote; remaining goes to "no" vote           |

## Vote Calculation

The system calculates the voter's contribution to the "yes" weight as:

```
yesContribution = (voterWeight × yesWeight) / 100
```

**Example:**

-   Voter weight: 200
-   `yesWeight`: 75
-   Contribution: (200 × 75) / 100 = 150 yes weight

Casting a new vote with the same parameters overwrites the previous vote, allowing voters to change their position.

## Examples

### Collection-Level Vote

```json
{
    "creator": "bb1voter123...",
    "collectionId": "1",
    "approvalLevel": "collection",
    "approverAddress": "",
    "approvalId": "governance-approval",
    "proposalId": "governance-proposal-1",
    "yesWeight": "100"
}
```

### User-Level Vote

```json
{
    "creator": "bb1voter123...",
    "collectionId": "1",
    "approvalLevel": "outgoing",
    "approverAddress": "bb1user456...",
    "approvalId": "delegation-approval",
    "proposalId": "delegation-proposal-1",
    "yesWeight": "80"
}
```

### Partial Vote

```json
{
    "creator": "bb1voter123...",
    "collectionId": "1",
    "approvalLevel": "collection",
    "approverAddress": "",
    "approvalId": "flexible-approval",
    "proposalId": "flexible-proposal-1",
    "yesWeight": "60"
}
```

This allocates 60% to "yes" and 40% to "no".

## Error Conditions

The message fails if:

-   `creator` is not in the voting challenge's voters list
-   `yesWeight` is greater than 100
-   `approvalLevel` is not `"collection"`, `"incoming"`, or `"outgoing"`
-   `proposalId` doesn't match any voting challenge in the specified approval
-   The approval with the specified ID doesn't exist

For details on voting challenges, see [Voting Challenges](../../token-standard/learn/approval-criteria/voting-challenges.md).

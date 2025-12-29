# Voting Challenges

Voting challenges require a weighted quorum threshold to be met through votes from specified voters. This enables multi-signature-like approval where multiple parties must vote to approve transfers, with each voter having a configurable weight. Votes are stored separately and can be updated.

The system requires a minimum percentage of total voter weight to vote "yes" before a transfer can be approved. Each voter has a weight, and each vote allocates a percentage (0-100%) to "yes" with the remainder going to "no".

## Structure

### Challenge Fields

| Field             | Type    | Description                                                      |
| ----------------- | ------- | ---------------------------------------------------------------- |
| `proposalId`      | string  | Unique identifier for tracking votes                             |
| `quorumThreshold` | string  | Percentage (0-100) of total possible weight that must vote "yes" |
| `voters`          | Voter[] | List of voters with addresses and weights                        |
| `uri`             | string  | Optional metadata URI                                            |
| `customData`      | string  | Optional custom data                                             |

### Vote Fields

| Field        | Type   | Description                                                   |
| ------------ | ------ | ------------------------------------------------------------- |
| `proposalId` | string | Proposal ID this vote is for                                  |
| `voter`      | string | Address of the voter casting the vote                         |
| `yesWeight`  | string | Percentage (0-100) allocated to "yes"; remainder goes to "no" |

## Voting Process

1. **Cast Vote**: Voters use `MsgCastVote` with `yesWeight` (0-100%)
2. **Store Vote**: Votes stored with key: `collectionId-approverAddress-approvalLevel-approvalId-proposalId-voterAddress`
3. **Update Vote**: Casting a new vote with same parameters overwrites the previous vote
4. **Calculate Threshold**: On transfer attempt:
    - Retrieve all votes for the proposal
    - Calculate each voter's yes contribution: `(voterWeight × yesWeight) / 100`
    - Sum all yes contributions
    - Calculate percentage: `(totalYesWeight × 100) / totalPossibleWeight`
    - Compare to `quorumThreshold`

## Weighted Voting

Each voter has a configurable weight:

```json
{
    "votingChallenges": [
        {
            "proposalId": "proposal-1",
            "quorumThreshold": "50",
            "voters": [
                { "address": "bb1abc...", "weight": "100" },
                { "address": "bb1def...", "weight": "200" },
                { "address": "bb1ghi...", "weight": "50" }
            ]
        }
    ]
}
```

**Example:**

-   Total possible weight: 350
-   Quorum threshold: 50% of 350 = 175 weight must vote "yes"

## Partial Votes

Voters can allocate a percentage to "yes" with the remainder going to "no":

| `yesWeight` | Yes % | No % |
| ----------- | ----- | ---- |
| `100`       | 100%  | 0%   |
| `70`        | 70%   | 30%  |
| `50`        | 50%   | 50%  |
| `0`         | 0%    | 100% |

## Threshold Calculation

**Important**: The threshold is calculated as a percentage of **total possible weight** (all voters), not just voters who have cast votes. Non-voting voters count as 0% yes.

**Example:**

-   Voter A: weight 100, votes 100% yes → contributes 100 yes weight
-   Voter B: weight 200, votes 50% yes → contributes 100 yes weight
-   Voter C: weight 50, doesn't vote → contributes 0 yes weight
-   Total possible weight: 350
-   Quorum threshold: 50%

**Calculation:**

-   Total yes weight: 100 + 100 + 0 = 200
-   Percentage: (200 × 100) / 350 = 57.14%
-   Result: 57.14% ≥ 50% → **Challenge satisfied**

## Implementation Details

### Vote Key Format

Votes are stored with key: `collectionId-approverAddress-approvalLevel-approvalId-proposalId-voterAddress`

This scopes votes to specific collection, approver, approval level, approval ID, proposal ID, and voter.

### Vote Verification

When checking if a challenge is satisfied:

1. Retrieve all votes for voters in the challenge
2. For each voter:
    - If vote exists: calculate yes contribution `(voterWeight × yesWeight) / 100`
    - If no vote: contribution is 0
3. Sum all yes contributions
4. Calculate percentage: `(totalYesWeight × 100) / totalPossibleWeight`
5. Compare to `quorumThreshold`

## Type Definitions

```typescript
interface VotingChallenge {
    proposalId: string; // Unique ID for tracking votes
    quorumThreshold: string; // Percentage (0-100) of total weight that must vote yes
    voters: Voter[]; // List of voters with their weights
    uri?: string; // Optional metadata URI
    customData?: string; // Optional custom data
}

interface Voter {
    address: string; // Address of the voter
    weight: string; // Weight of this voter's vote
}
```

Cast votes using [MsgCastVote](../../x-badges/messages/msg-cast-vote.md).

## Use Cases

### Multi-Signature Approval

Require unanimous approval from multiple parties:

```json
{
    "votingChallenges": [
        {
            "proposalId": "multisig-1",
            "quorumThreshold": "100",
            "voters": [
                { "address": "bb1alice...", "weight": "1" },
                { "address": "bb1bob...", "weight": "1" },
                { "address": "bb1charlie...", "weight": "1" }
            ]
        }
    ]
}
```

Requires all three voters to vote 100% yes.

### Weighted Governance

Different stakeholders have different voting power:

```json
{
    "votingChallenges": [
        {
            "proposalId": "governance-1",
            "quorumThreshold": "66",
            "voters": [
                { "address": "bb1founder...", "weight": "1000" },
                { "address": "bb1investor...", "weight": "500" },
                { "address": "bb1community...", "weight": "100" }
            ]
        }
    ]
}
```

Requires 66% of total weight (1056 out of 1600) to vote yes.

### Flexible Approval

Lower threshold for partial approval:

```json
{
    "votingChallenges": [
        {
            "proposalId": "flexible-1",
            "quorumThreshold": "30",
            "voters": [
                { "address": "bb1voter1...", "weight": "100" },
                { "address": "bb1voter2...", "weight": "100" },
                { "address": "bb1voter3...", "weight": "100" }
            ]
        }
    ]
}
```

Allows approval with 30% of total weight voting yes.

## Error Conditions

The system fails if:

-   Voter is not in the challenge's voters list
-   `yesWeight` is greater than 100
-   Percentage of yes votes is less than the quorum threshold
-   `proposalId` doesn't match any voting challenge in the approval
-   Challenge has no voters or all voters have zero weight

## Security Considerations

### Proposal ID Management

Changing the `proposalId` resets the vote tracker. Use unique proposal IDs for each challenge to prevent vote overlap. Useful for rotating challenge configurations or creating new voting periods, but ensure old votes cannot be maliciously reused.

### Vote Tampering Prevention

Votes are stored on-chain and cannot be tampered with. Votes can be updated by the voter, are scoped to specific contexts (preventing cross-context reuse), and the system validates voters are in the challenge's voters list.

### Abstention Handling

Non-voting voters are treated as 0% yes (100% no). Set realistic thresholds that account for expected participation. High thresholds with many voters may be difficult to meet if voters abstain.

### Weight Distribution

Weight distribution affects security. If one voter has most of the weight, they can control approvals. Distribute weights appropriately to prevent single points of failure. Use equal weights for multi-signature scenarios.

Use the `uri` and `customData` fields to provide context about what voters are approving. Monitor vote tallies to understand approval status.

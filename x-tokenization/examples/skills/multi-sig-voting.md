# Multi-Sig / Voting

> Require weighted quorum voting from multiple parties before transfers can proceed (multi-sig, governance, etc.)

**Category:** Approval Patterns

## Summary

Enables multi-signature-like approval via votingChallenges[] in approvalCriteria.

- Each voter has an address and a weight
- quorumThreshold: percentage (0-100) of total possible weight that must vote "yes"
- Voters cast votes via MsgCastVote with yesWeight (0-100%)
- Non-voting voters count as 0% yes; threshold is % of ALL voters' total weight, not just those who voted
- Votes can be updated (re-casting overwrites previous vote)
- proposalId: unique identifier — changing it resets the vote tracker
- Vote state does not reset after quorum met — designed for one-time transfers
- Common patterns: unanimous (threshold: "100", equal weights), majority (threshold: "51"), weighted governance

## Instructions

## Multi-Sig / Voting Challenges

### Concept

Voting challenges enable multi-signature-like approval where multiple parties must vote before a transfer can proceed. Each voter has a configurable weight, and a quorum threshold (percentage of total weight) must be met. This is configured via the `votingChallenges[]` field in `approvalCriteria`.

### How It Works

1. An approval has `votingChallenges` with a list of voters, weights, and a quorum threshold
2. Voters cast votes using `MsgCastVote` with a `yesWeight` (0-100%)
3. When a transfer is attempted, the system checks if the quorum threshold is met
4. Threshold is calculated as a percentage of **total possible weight** (all voters), not just those who voted
5. Non-voting voters count as 0% yes

### Structure

```json
{
  "votingChallenges": [
    {
      "proposalId": "proposal-1",
      "quorumThreshold": "50",
      "voters": [
        { "address": "bb1alice...", "weight": "100" },
        { "address": "bb1bob...", "weight": "200" },
        { "address": "bb1charlie...", "weight": "50" }
      ],
      "uri": "",
      "customData": ""
    }
  ]
}
```

### Key Fields

- **proposalId**: Unique identifier for tracking votes. Changing it resets the vote tracker.
- **quorumThreshold**: Percentage (0-100) of total possible weight that must vote "yes"
- **voters**: List of voter addresses with their weights
- **yesWeight** (in MsgCastVote): Percentage (0-100%) allocated to "yes"; remainder goes to "no"

### Common Patterns

#### Multi-Sig (Unanimous)
All parties must approve. Set `quorumThreshold: "100"` with equal weights:
```json
{ "quorumThreshold": "100", "voters": [
  { "address": "bb1a...", "weight": "1" },
  { "address": "bb1b...", "weight": "1" },
  { "address": "bb1c...", "weight": "1" }
]}
```

#### Majority Vote
Require >50% approval:
```json
{ "quorumThreshold": "51", "voters": [
  { "address": "bb1a...", "weight": "1" },
  { "address": "bb1b...", "weight": "1" },
  { "address": "bb1c...", "weight": "1" }
]}
```

#### Weighted Governance
Different stakeholders have different voting power:
```json
{ "quorumThreshold": "66", "voters": [
  { "address": "bb1founder...", "weight": "1000" },
  { "address": "bb1investor...", "weight": "500" },
  { "address": "bb1community...", "weight": "100" }
]}
```

### Threshold Calculation Example

- Voter A: weight 100, votes 100% yes → contributes 100
- Voter B: weight 200, votes 50% yes → contributes 100
- Voter C: weight 50, doesn't vote → contributes 0
- Total possible weight: 350
- Total yes weight: 200
- Percentage: (200 × 100) / 350 = 57.14%
- If quorumThreshold is 50 → **satisfied**

### Important Notes

- Votes are cast via `MsgCastVote` — a separate transaction from the transfer itself
- Votes can be updated (re-casting overwrites the previous vote)
- Vote keys are scoped: `collectionId-approverAddress-approvalLevel-approvalId-proposalId-voterAddress`
- Set realistic thresholds — high thresholds with many voters may be hard to meet if voters abstain
- **Vote state does not reset** — votingChallenges are scoped to the approval and are typically used for one-time transfers (e.g., releasing funds once). Once the quorum is met, the approval remains satisfied. For more advanced recurring multi-sig solutions, you may need to get creative with other primitives (e.g., rotating proposalIds, dynamic stores, or manager-controlled approval updates).
- For full documentation, see the BitBadges docs on voting challenges
